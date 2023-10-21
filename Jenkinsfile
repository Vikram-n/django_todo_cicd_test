pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'github_login', 
                url: 'https://github.com/Vikram-n/django_todo_cicd_test.git',
                branch: 'develop'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t vikram5/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push vikram5/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'github_login', 
                url: 'https://github.com/Vikram-n/django_todo_cicd_test.git',
                branch: 'develop'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'github_login', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deployment.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deployment.yaml
                        cat deployment.yaml
                        git add deployment.yaml
                        git commit -m 'Updated the deployment yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/vikram-n/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
