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
                sh "docker run -p 90:80 -d rajeshtalla0209/votingapp-vote:${VERSION}"
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
                sh "docker run -p 70:80 -d rajeshtalla0209/votingapp-result:${VERSION}"
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
    }
}
// final success one 