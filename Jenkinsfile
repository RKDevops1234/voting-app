pipeline {
    agent any

    environment {
        VERSION = '1'
        S3_BUCKET = 'rajtal-votingapp'
        S3_PATH = 'helm-charts'
        AWS_REGION = 'us-east-1' // Specify your AWS region here
        CHART_VERSION = "1.1.2"
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
        
        stage('Package Helm Chart') {
            steps {
                dir('voting-app/db/charts') {
                    sh 'helm package . --version ${CHART_VERSION}'
                }
            }
        }

        stage('Upload Helm Chart to S3') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials-id']]) {
                    script {
                    // List all Helm chart files
                    def chartFiles = sh(script: 'ls voting-app/db/charts/*.tgz', returnStdout: true).trim().split('\n')
                
                    // Debug output to verify chartFiles
                    echo "Chart files found: ${chartFiles.join(', ')}"
                
                    // Upload each Helm chart file to S3
                        chartFiles.each { chartFile ->
                        echo "Uploading ${chartFile} to S3"
                        sh """
                            aws s3 cp "${chartFile}" s3://${S3_BUCKET}/${S3_PATH}/ --region ${AWS_REGION}
                        """
                    }
                }
            }
        
            }
        }
    }  
        post {
        always {
            // Clean up Docker images and workspace directory
            sh 'docker image prune -f'
            sh 'docker system prune -f'
            // Manually clean workspace directory
            sh 'rm -rf $WORKSPACE/*'
        }
        success {
            echo 'Build succeeded!'
            // Additional actions on success 
        }
        failure {
            echo 'Build failed!'
             //Clean workspace even on failure, but it's already covered in 'always'
        }
         }
  }
        
// Success one

    
       