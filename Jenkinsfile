pipeline {
   agent any

   environment {
     
     SERVICE_NAME = "fleetman-api-gateway"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
	  
      stage('Build') {
         steps {
            sh '''mvn clean package'''
         }
      }
	  
	  stage('SD Elements Check') {
		steps {
        build job: 'SD Elements', wait: true
		}
	  }

	  stage('Static Security Scan') {
         steps {
            echo 'Running Security Scan'
         }
      }
	  
     
	 stage('Build and Push Dock Image') {
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} .'
         }
      }
	  
      stage('Deploy to Kubernetes Cluster') {
          steps {
                    sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
          }
      }
	  
	  stage('Automation Tests') {
         steps {
            echo 'Running Automation Tests'
         }
      }
	  
	  stage('Runtime Security Tests') {
         steps {
            echo 'Running Automation Tests'
         }
      }
	  
   }
   
	post {
		success {
			echo 'Success!'
		}
		failure {
			echo 'Failed'
		}
	}
   
}
