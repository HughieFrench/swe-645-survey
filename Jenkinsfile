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
					dockerImage = docker.build("${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}")
				}
			}
		}
		stage('Push to Docker Hub') {
			steps {
				script {
					docker.withRegistry('https://registry-1.docker.io', 'docker-pass') {
						dockerImage.push()
					}
				}
			}
		}
		stage('Update Kubernetes deployment') {
			steps {
				script {
					withKubeConfig([
						credentialsId: 'kubeconfig-id'
					]) {
						sh """
						sed -i 's|<YOUR_DOCKERHUB_USERNAME>/ubuntu-nginx-survey:latest|${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}|' nginx-survey-deployment.yaml
						kubectl apply -f nginx-survey-deployment.yaml
						kubectl apply -f nginx-survey-service.yaml
						"""
					}
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
