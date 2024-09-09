pipeline {
    agent any

    environment {
        VERSION = '1'
    }

    stages {
        stage('Build-vote') {
            steps {
                // Checkout the code
                git branch: 'main',
                    credentialsId: 'your-credentials-id',
                    url: 'https://github.com/RKDevops1234/votingapp-vote.git'

                // Print the Git repository information
                sh 'git remote -v'
                sh 'git branch -a'
                sh 'git status'

                // Build the Docker image
                sh "docker build -t rajeshtalla0209/votingapp-vote:${VERSION} ."
            }
        }

        stage('Run-vote') {
            steps {
                // Run the Docker container and expose port 80
                sh 'docker rm /votingapp-vote || true'
                sh "docker run -p 90:80 -d --name votingapp-vote rajeshtalla0209/votingapp-vote:${VERSION}"
            }
        }
        stage('Stop Docker Container vote') {
            steps {
                sh 'docker ps -aq --filter "name=votingapp-vote" | xargs docker stop'
            }
        }
        stage('Push to Docker Hub - vote') {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'DOCKER_HUB_TOKEN')]) {
                    // Log in to Docker Hub using a token
                    sh "echo ${DOCKER_HUB_TOKEN} | docker login -u rajeshtalla0209 --password-stdin https://registry.hub.docker.com"

                    // Tag the image with your Docker Hub username and repository name
                    sh "docker tag rajeshtalla0209/votingapp-vote:${VERSION} rajeshtalla0209/votingapp-vote:${VERSION}"

                    // Push the image to Docker Hub
                    sh "docker push rajeshtalla0209/votingapp-vote:${VERSION}"
                }
            }
        }
        stage('Build-result') {
            steps {
                // Checkout the code
                git branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/RKDevops1234/votingapp-result.git'

                // Print the Git repository information
                sh 'git remote -v'
                sh 'git branch -a'
                sh 'git status'

                // Build the Docker image
                sh "docker build -t rajeshtalla0209/votingapp-result:${VERSION} ."
            }
        }

        stage('Run-result') {
            steps {
                // Run the Docker container and expose port 80
                sh 'docker rm /votingapp-result || true'
                sh "docker run -p 70:80 -d --name votingapp-result rajeshtalla0209/votingapp-result:${VERSION}"
            }
        }
        stage('Stop Docker Container- result') {
            steps {
                sh 'docker ps -aq --filter "name=votingapp-result" | xargs docker stop'
            }
        }

        stage('Push to Docker Hub - result') {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'DOCKER_HUB_TOKEN')]) {
                    // Log in to Docker Hub using a token
                    sh "echo ${DOCKER_HUB_TOKEN} | docker login -u rajeshtalla0209 --password-stdin https://registry.hub.docker.com"

                    // Tag the image with your Docker Hub username and repository name
                    sh "docker tag rajeshtalla0209/votingapp-result:${VERSION} rajeshtalla0209/votingapp-result:${VERSION}"

                    // Push the image to Docker Hub
                    sh "docker push rajeshtalla0209/votingapp-result:${VERSION}"
                }
            }
        }
        stage('Build-worker') {
            steps {
                // Checkout the code
                git branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/RKDevops1234/votingapp-worker.git'

                // Print the Git repository information
                sh 'git remote -v'
                sh 'git branch -a'
                sh 'git status'

                // Build the Docker image
                sh "docker build -t rajeshtalla0209/votingapp-worker:${VERSION} ."
            }
        }

        stage('Run-worker') {
            steps {
                // Run the Docker container and expose port 80
                sh 'docker rm /votingapp-worker || true'
                sh "docker run -p 83:80 -d --name votingapp-worker rajeshtalla0209/votingapp-worker:${VERSION}"
            }
        }

        stage('Stop Docker Container- worker') {
            steps {
                sh 'docker ps -aq --filter "name=votingapp-worker" | xargs docker stop'
            }
        }

        stage('Push to Docker Hub-worker') {
            steps {
                withCredentials([string(credentialsId: 'DOCKER_HUB_TOKEN', variable: 'DOCKER_HUB_TOKEN')]) {
                    // Log in to Docker Hub using a token
                    sh "echo ${DOCKER_HUB_TOKEN} | docker login -u rajeshtalla0209 --password-stdin https://registry.hub.docker.com"

                    // Tag the image with your Docker Hub username and repository name
                    sh "docker tag rajeshtalla0209/votingapp-worker:${VERSION} rajeshtalla0209/votingapp-worker:${VERSION}"

                    // Push the image to Docker Hub
                    sh "docker push rajeshtalla0209/votingapp-worker:${VERSION}"
                }
            }
        }
        
        stage('Clone Repository') {
            steps {
                sh 'git clone https://github.com/RKDevops1234/voting-app.git'
            }
        }
        stage('Package Helm chart') {
            steps {
                sh 'cd voting-app/db/charts && helm package .'
            }
        }
        
        stage('Push Helm chart to AWS CodeArtifact') {
            steps {
                withAWS (credentials: 'aws-credentials-id') {
                // Configure the AWS CLI
                sh "aws configure set default.region us-east-1"

                // Create a repository in AWS CodeArtifact
                //sh "aws codeartifact create-repository --repository my-repo --domain my-domain"

                // Push the Helm chart to AWS CodeArtifact
                //sh "aws codeartifact put-package-origin-configuration --repository voting-app --domain petclinic --format generic --restrictions '{\"packageVersion\": \"0.1.0\"}'" --package my-chart-0.1.0.tgz"
                sh """
                        aws codeartifact put-package-origin-configuration --repository voting-app --domain petclinic --package my-chart-0.1.0.tgz --format helm --restrictions '{"packageVersion": "0.1.0"}'
                    """
                }
        post {
        cleanup {
            sh 'docker image prune -f'
            sh 'docker system prune -f'
            cleanWs()
        }
    }
     }
 }

}
}

    
       