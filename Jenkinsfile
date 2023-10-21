pipeline {
    agent any 

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: 'develop']], 
                          userRemoteConfigs: [[credentialsId: 'github_login', url: 'https://github.com/Vikram-n/django_todo_cicd_test.git']]
                ])
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    def dockerImage = docker.build("vikram5/cicd-e2e:${BUILD_NUMBER}", ".")
                }
            }
        }

        stage('Push the artifacts') {
            steps {
                script {
                    def dockerImage = docker.image("vikram5/cicd-e2e:${BUILD_NUMBER}")
                    dockerImage.push()
                }
            }
        }

        stage('Checkout K8S manifest SCM') {
            steps {
                checkout([$class: 'GitSCM', 
                          branches: [[name: 'develop']], 
                          userRemoteConfigs: [[credentialsId: 'github_login', url: 'https://github.com/Vikram-n/django_todo_cicd_test.git']]
                ])
            }
        }

        script{
                    withCredentials([usernamePassword(credentialsId: 'github_login', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/vikram-n/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                        }
                    }
                }
            }
        }
    }
}


