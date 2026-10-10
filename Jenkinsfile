pipeline {
	agent any

	environment {
		DOCKERHUB_PASS = credentials('docker-pass')
		DOCKERHUB_USER = 'hfrench5'
		IMAGE_NAME = 'ubuntu-nginx-survey'
		IMAGE_TAG = 'latest'
	}
	stages {
		stage('Checkout') {
			steps {
				checkout scm
			}
		}
		stage('Build Docker image') {
			steps {
				script {
					sh "docker login -u ${DOCKERHUB_USER} -p $DOCKERHUB_PASS"
					dockerImage = docker.build("${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}")
				}
			}
		}
		stage('Push to Docker Hub') {
			steps {
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'docker-pass') {
						dockerImage.push()
					}
				}
			}
		}
		stage('Update Kubernetes deployment') {
			steps {
				script {
					sh """
					sed -i 's|<YOUR_DOCKERHUB_USERNAME>/ubuntu-nginx-survey:latest|${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}|' k8s/deployment.yaml
					kubectl apply -f k8s/deployment.yaml
					kubectl apply -f k8s/service.yaml
					"""
				}
			}
		}
	}
	post {
		success {
			echo 'Deployment succesful!'
		}
		failure {
			echo 'Deployment failed.'
		}
	}
}
