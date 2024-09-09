pipeline {
    agent any

    environment {
        VERSION = '1'
        DOCKER_HUB_USERNAME = 'rajeshtalla0209'
        DOCKER_HUB_TOKEN = credentials('DOCKER_HUB_TOKEN')
        COMPONENTS = ['vote', 'result', 'worker']
        HELM_CHARTS = ['vote', 'result', 'worker']
        CODEARTIFACT_DOMAIN = 'petclinic'
        CODEARTIFACT_REPOSITORY = 'voting-app'
        CODEARTIFACT_REGION = 'us-east-1'
        //AWS_ACCESS_KEY_ID = 'your-aws-access-key-id'
        //AWS_SECRET_ACCESS_KEY = 'your-aws-secret-access-key'
    }

    stages {
        stage('Login to Docker Hub') {
            steps {
                sh "echo ${DOCKER_HUB_TOKEN} | docker login -u ${DOCKER_HUB_USERNAME} --password-stdin https://registry.hub.docker.com"
            }
        }

        stage('Build and Deploy') {
            steps {
                script {
                    for (component in COMPONENTS) {
                        def repositoryUrl = "https://github.com/RKDevops1234/votingapp-${component}.git"
                        def dockerImageName = "${DOCKER_HUB_USERNAME}/votingapp-${component}:${VERSION}"

                        // Checkout the code
                        git branch: 'main',
                            credentialsId: 'github',
                            url: repositoryUrl

                        // Print the Git repository information
                        sh 'git remote -v'
                        sh 'git branch -a'
                        sh 'git status'

                        // Build the Docker image
                        sh "docker build -t ${dockerImageName} ."

                        // Run the Docker container and expose port 80
                        def hostPort = component == 'vote' ? 90 : component == 'result' ? 70 : 83
                        sh "docker run -p ${hostPort}:80 -d ${dockerImageName}"

                        // Push the image to Docker Hub
                        sh "docker tag ${dockerImageName} ${dockerImageName}"
                        sh "docker push ${dockerImageName}"
                    }
                }
            }
        }
        stage('Build and Package Helm Chart') {
            steps {
                // Build and package your Helm chart here
                sh 'helm package db'
            }
        }        
    }
}   
       