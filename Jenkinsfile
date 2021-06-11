pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)

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

	  stage('Security Scan') {
         steps {
            echo 'Running Security Scan'
         }
      }
	  
	  stage("SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('Niyam-Sonar') {
                sh 'mvn clean sonar:sonar'
              }
            }
		}
     
	 stage('Build and Push Image') {
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} .'
         }
      }
	  
      stage('Deploy to Cluster') {
          steps {
                    sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
          }
      }
	  
	  stage('Automation Tests') {
         steps {
            echo 'Running Automation Tests'
         }
      }
	  
	  stage('SD Elements Query') {
		steps {
        build job: 'SD Elements', wait: true
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
