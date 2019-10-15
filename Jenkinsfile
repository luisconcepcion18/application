pipeline {
    agent any
    environment {
        //be sure to replace "luisconcepcion18" with your own Docker Hub username 
        DOCKER_IMAGE_NAME = "timeoff"
    }
    stages {
       
        stage('Build Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    app = docker.build(DOCKER_IMAGE_NAME) //Create and tag image with the name luisconcepcion18/timeoff-management-gorilla using the Dockerfile in the repository
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            when {
                branch 'master'
            }
            steps {
                script {
                    docker.withRegistry('https://744193803837.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                //implement Kubernetes deployment here
		kubernetesDeploy(
                    kubeconfigId: 'kubeconfig',
                    configs: 'timeoff-management-gorilla.yml',
                    enableConfigSubstitution: true
                )
            }
        }
    }
}
