pipeline {
    agent any
	environment {
        AWS_REGION = 'us-east-1'
        AWS_CREDENTIALS = 'aws'
        DOCKER_HUB_CREDENTIALS = 'dockerhub'
		CLUSTER_NAME = 'udacity2'
    }
    stages {
 
        stage('Lint HTML') {
			steps {
				sh 'ls deploy/'
				sh 'cat deploy/index.html'
				sh 'tidy -q -e deploy/*.html'
			}
		}
		
		stage('Docker image - build') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker build -f deploy/Dockerfile -t elmokdad/capstone .
					'''
				}
			}
		}

        stage('Docker image - push') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
						docker push elmokdad/capstone
					'''
				}
			}
		}
	    

        stage('Blue container - deploy') {
			steps {
				withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
					sh '''
						kubectl apply -f ./deploy/blue-controller.json
					'''
				}
			}
		}

        stage('Green container - deploy') {
			steps {
				withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
					sh '''
						kubectl apply -f ./deploy/green-controller.json
					'''
				}
			}
		}

        stage('Blue service - deploy') {
			steps {
				withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
					sh '''
						kubectl apply -f ./deploy/blue-service.json
					'''
				}
			}
		}

        stage('Wait user approve') {
            steps {
                input "Do you want to switch traffic to green service?"
            }
        }

        stage('Green service - deploy') {
			steps {
				withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
					sh '''
						kubectl apply -f ./deploy/green-service.json
					'''
				}
			}
		}

		stage("Docker clean") {
            steps {
                script {
                    sh "docker system prune --force"
                }
            }
        }    
    }
}

