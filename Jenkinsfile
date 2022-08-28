pipeline {
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{

        stage("docker build & docker push"){
            agent {
                label 'azure'
            }
                steps{
                    script{
                        withCredentials([string(credentialsId: 'docker_secret', variable: 'docker_secret')]) {
                            dir('client/') {
                                sh '''
                                docker build -t bill3213/springapp:${VERSION} .
                                docker rmi bill3213/springapp:${VERSION}
                                '''
                            }
                            dir('server/') {
                                sh '''
                                docker build -t bill3213/springapp:${VERSION} .
                                docker rmi bill3213/springapp:${VERSION}
                                '''
                            }
                        }
                    }
                }
        }
        stage('Deploying application on k8s cluster') {
            steps {
                dir('k8s/') {
                    sh 'kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml'
                    sh 'kubectl apply -f .'
                }
            }
        }
    }
    post {
		always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "markwin1999@gmail.com";
		}
	}
}
