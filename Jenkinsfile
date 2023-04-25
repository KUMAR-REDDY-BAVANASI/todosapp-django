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

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t kumar5483/todoapp:${IMAGE_TAG} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push kumar5483/todoapp:${IMAGE_TAG}
                    '''
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
                    cat python-jenkins-argocd-k8s/manifests/deploy.yaml
                    sed -i '' "s/replaceImageTag/${IMAGE_TAG}/g" python-jenkins-argocd-k8s/manifests/deploy.yaml
                    cat python-jenkins-argocd-k8s/manifests/deploy.yaml
                    git add python-jenkins-argocd-k8s/manifests/deploy.yaml
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