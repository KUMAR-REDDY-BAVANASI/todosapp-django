pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                sh 'echo passed'
                // git branch: 'main', url: 'https://github.com/KUMAR-REDDY-BAVANASI/todosapp-django.git'
           }
        }

        stage('Build and Push Docker Image') {
          environment {
            DOCKER_IMAGE = "kumar5483/todoapp:${IMAGE_TAG}"
            // DOCKERFILE_LOCATION = "Dockerfile"
            REGISTRY_CREDENTIALS = credentials('docker-cred')
          }
          steps {
            script {
                sh 'docker build -t ${DOCKER_IMAGE} .'
                def dockerImage = docker.image("${DOCKER_IMAGE}")
                docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                    dockerImage.push()
                }
            }
          }
        }

        stage('Update K8S manifest & push to Repo'){
            environment {
                GIT_REPO_NAME = "todosapp-django"
                GIT_USER_NAME = "KUMAR-REDDY-BAVANASI"
            }
            steps {
                withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                    sh '''
                    git config user.email "kumar5483reddy@gmail.com"
                    git config user.name "KUMAR-REDDY-BAVANASI"
                    cat manifests/deploy.yaml
                    sed -i '' "s/replaceImageTag/${IMAGE_TAG}/g" manifests/deploy.yaml
                    cat manifests/deploy.yaml
                    git add manifests/deploy.yaml
                    git commit -m "Update deployment image to version ${IMAGE_TAG}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                }
            }
        }
    }
    post { 
        always { 
            cleanWs()
        }
    }
}
