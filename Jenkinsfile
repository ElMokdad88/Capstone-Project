pipeline {
    agent any
	environment {
        AWS_REGION = 'us-west-2'
        AWS_CREDENTIALS = 'aws-kubernetes'
        DOCKER_HUB_CREDENTIALS = 'dockerhub_credentials'
		CLUSTER_NAME = 'EmaJarK8sClusterCapstone'
    }
    stages {
        stage('Kubernetes cluster') {
			steps {
				withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
					sh '''

						if aws cloudformation describe-stacks --stack-name eksctl-${CLUSTER_NAME}-cluster; then
							echo 'Stack already exists'
						else
							echo 'Stack is being created'
							eksctl create cluster \
							--name ${CLUSTER_NAME} \
							--version 1.14 \
							--nodegroup-name standard-workers \
							--node-type t2.small \
							--nodes 2 \
							--nodes-min 1 \
							--nodes-max 3 \
							--node-ami auto \
							--region us-west-2 \
							--zones us-west-2a \
							--zones us-west-2b \
							--zones us-west-2c \

							which aws
							aws --version
							hostname
						fi
					'''
				}
			}
		}

        stage('Configuration file cluster') {
			steps {
				withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
					sh '''
						aws eks --region us-west-2 update-kubeconfig --name ${CLUSTER_NAME}
					'''
				}
			}
		}

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
						docker build -f deploy/Dockerfile -t emajar/udacity_capstone .
					'''
				}
			}
		}

        stage('Docker image - push') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
						docker push emajar/udacity_capstone
					'''
				}
			}
		}

        stage('Set kubectl context') {
			steps {
				withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
					sh '''
                        kubectl config get-contexts
						kubectl config use-context arn:aws:eks:us-west-2:350027292717:cluster/${CLUSTER_NAME}
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

		// Optional debugging step used to print information about the cluster
		// stage('Debug') {
		// 	steps {
		// 		withAWS(region:"${AWS_REGION}", credentials:"${AWS_CREDENTIALS}") {
		// 			sh '''
		// 				kubectl get services
		// 				kubectl get pods
		// 				kubectl describe pods
		// 			'''
		// 		}
		// 	}
		// }
    }
}

