
def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline{

	agent any

	environment {
		DOCKERHUB_CREDENTIALS=credentials('DOCKERHUB_CREDENTIALS')
	    AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')
    	AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
		IMAGE_REPO_NAME = "michaelgwei86/effulgencetech-nodejs-img"
		CONTAINER_NAME= "effulgencetech-nodejs-cont-"
		ECR_REPO_NAME = "effulgencetech-nodejs-repo"
		REPOSITORY_URI = credentials('ecr-repo-uri')
		AWS_DEFAULT_REGION = "us-west-2"
	}

	stages {

		stage('Build-Image') {
			//Building and tagging our Docker image
			//rename the user name michaelgwei86 with the username of your dockerhub repo
			steps {
				//Building image for Dockerhub repo
				//sh 'docker build -t michaelgwei86/effulgencetech-nodejs-image:$BUILD_NUMBER .'
				sh 'docker build -t $IMAGE_REPO_NAME:$BUILD_NUMBER .'

				//Building image for ECR repo

				//sh 'docker build -t $REPOSITORY_URI/$IMAGE_REPO_NAME:$BUILD_NUMBER .'

				sh 'docker build -t $ECR_REPO_NAME:$BUILD_NUMBER .'
				sh 'docker tag $ECR_REPO_NAME:$BUILD_NUMBER $REPOSITORY_URI/$ECR_REPO_NAME:latest'
				sh 'docker images'
			}
		}

		stage('Login to Dockerhub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Build-Container') {
			//Building and tagging our Docker container
			//rename the user name michaelgwei86 with the username of your dockerhub repo
			steps {
				//sh 'docker run --name effulgencetech-node-cont-$BUILD_NUMBER -p 8082:8080 -d michaelgwei86/effulgencetech-nodejs-image:$BUILD_NUMBER'
			//	sh 'docker run --name $CONTAINER_NAME-$BUILD_NUMBER -p 8087:8080 -d $IMAGE_REPO_NAME:$BUILD_NUMBER'
				sh 'docker ps'
			}
		}


		stage('Push to Dockerhub') {
			//Pushing image to dockerhub
			steps {
				//sh 'docker push michaelgwei86/effulgencetech-nodejs-image:$BUILD_NUMBER'
				sh 'docker push $IMAGE_REPO_NAME:$BUILD_NUMBER'
			}
		}

        stage('Push to ECR') {
            steps {
				
				//Logging into ECR repository
				sh 'aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $REPOSITORY_URI'
			
				//pushing image to ECR Repository

				sh 'docker push $REPOSITORY_URI/$ECR_REPO_NAME:latest'

				//sh 'docker push $REPOSITORY_URI/$IMAGE_REPO_NAME:$BUILD_NUMBER'
                
				}   
            }
        
	}

    post { 
        always { 
            echo 'I will always say Hello again!'
            slackSend channel: '#effulgencetech-devops-channel', color: COLOR_MAP[currentBuild.currentResult], message: "*${currentBuild.currentResult}:*, Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }

}