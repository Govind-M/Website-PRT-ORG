pipeline {
    agent {label 'Kubernetes Agent'}
    
    triggers {
        githubPush()  
    }
    
    environment {
	DOCKER_USERNAME = 'manojgovind'
        IMAGE_NAME = "my-latest-apache-website-image"
	IMAGE_TAG = 'latest'
	IMAGE = "${DOCKER_USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}"
        CONTAINER_NAME = "my-latet-apache-website-container"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch:'main', url:'https://github.com/Govind-M/Website-PRT-ORG.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build --no-cache -t $IMAGE -f dockerfile.txt .'
            }
        }
	stage('Push to DockerHub') {
      		steps {
        		withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKER_PASS')]) {
          		sh '''
            		echo $DOCKER_PASS | docker login -u $DOCKER_USERNAME --password-stdin
            		docker push $IMAGE
          		'''
       			 	}
     		 	   }
			}
	 stage('Deploy to Kubernetes') {
      		steps {
        		sh '''
			envsubst < deployment.yaml.template > deployment.yaml
          		kubectl apply -f deployment.yaml
          		kubectl apply -f service.yaml
        		'''
      			}
    		}
        }
}
