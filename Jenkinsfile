pipeline{
    agent any

    tools{
        maven "maven"
    }
    
    environment{
        APP_NAME = "crud-using-k8s"
        RELEASE_NO= "1.0.0"
        DOCKER_USER= "dwivediji"
        IMAGE_NAME= "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG= "${RELEASE_NO}-${BUILD_NUMBER}"
    }

    stages{

        stage("SCM checkout"){
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Dwivedi-Vaibhav/springboot-crud-using-k8s']])
            }
        }

        stage("Build Process"){
            steps{
                script{
                    bat 'mvn clean install -DskipTests=true'
                }
            }
        }
        
        /*stage("Login in to docker hub"){
            steps{
                withCredentials([string(credentialsId: 'mera-token', variable: 'mera-token')]) {
                bat 'docker login -u dwivediji -p %mera-token%'
                }
            }
        }*/
    
        stage("Build & TAG Docker Image"){
            steps{
                script{
                    //bat 'docker build -t dwivediji/spring-cicda:3.0 .'
                    bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
                }
            }
        }
    
        stage("Push Docker image to docker Hub"){
            steps{
                withCredentials([string(credentialsId: 'mera-token', variable: 'mera-token')]) {
                bat 'docker login -u dwivediji -p %mera-token%'
               // bat 'docker push dwivediji/spring-cicda:3.0'
                bat 'docker push %IMAGE_NAME%:%IMAGE_TAG%'
                }
            }
        }
		
		stage("Deploy to Kubernetes") {
            steps {
                script {
                    kubeconfig(credentialsId: 'kubeConfig-29-05', serverUrl: '') {
                        bat """powershell -Command "(Get-Content k8s-app.yaml) -replace 'image: .*', 'image: ${IMAGE_NAME}:${IMAGE_TAG}' | Set-Content k8s-app.yaml" """
                        bat 'kubectl apply -f k8s-secrets.yaml'
                        bat 'kubectl apply -f k8s-config.yaml'
                        bat 'kubectl apply -f k8s-db.yaml'
                        bat 'kubectl apply -f k8s-app.yaml'
                    }
                }
            }
        }

        stage("Verify deployment") {
            steps {
                script {
                    kubeconfig(credentialsId: 'kubeConfig-29-05', serverUrl: '') {
                        // Check deployed pods
                        bat 'kubectl get pods'
                    }
                }
            }
        }

    }

    post{
        always{
            emailext attachLog: true,
            body: ''' <html>
    <body>
        <p>Build Status: ${BUILD_STATUS}</p>
        <p>Build Number: ${BUILD_NUMBER}</p>
        <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
    </body>
    </html>''', mimeType: 'text/html', replyTo: 'doraemonsrivastava007@gmail.com', subject: 'Pipeline Status : ${BUILD_NUMBER}', to: 'doraemonsrivastava007@gmail.com'

        }
    }
}
