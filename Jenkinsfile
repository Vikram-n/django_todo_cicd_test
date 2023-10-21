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

        stage('Update K8S manifest & push to Repo') {
    steps {
        script {
            withCredentials([usernamePassword(credentialsId: 'github_login', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                dir('k8s') {
                    // Read the deploy.yaml file
                    def yamlFile = readFile('deploy.yaml')

                    // Perform the replacement using Groovy string interpolation
                    def updatedYaml = yamlFile.replaceAll('32', "${BUILD_NUMBER}")

                    // Write the updated YAML content back to the file
                    writeFile(file: 'deploy.yaml', text: updatedYaml)

                    // Now commit and push
                    sh '''
                    git add deploy.yaml
                    git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                    git push https://github.com/vikram-n/cicd-demo-manifests-repo.git HEAD:main
                    '''
                }
            }
        }
    }
}

}
