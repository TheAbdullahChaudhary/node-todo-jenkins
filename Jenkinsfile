pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "node-app-test-new"
        DOCKER_TAG = "latest"
        DOCKER_HUB_REPO = "${env.dockerHubUser}/node-app-test-new"
    }

    stages {
        stage("Checkout Code") {
            steps {
                // Checkout the latest code from the repository
                git url: "https://github.com/TheAbdullahChaudhary/node-todo-jenkins.git", branch: "master"
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    // Build the Docker image with the tag and commit hash (if you need versioning)
                    sh "docker build . -t ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }

        stage("Test Docker Image") {
            steps {
                script {
                    // Optional: Run tests or checks inside the container
                    sh "docker run --rm ${DOCKER_IMAGE}:${DOCKER_TAG} npm test"  // Adjust if you have tests
                }
            }
        }

        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: "dockerhub-credentials", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
                    script {
                        // Login to Docker Hub
                        sh "echo ${dockerHubPass} | docker login -u ${dockerHubUser} --password-stdin"

                        // Tag the Docker image with the Docker Hub repo
                        sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_HUB_REPO}:${DOCKER_TAG}"

                        // Push the Docker image to Docker Hub
                        sh "docker push ${DOCKER_HUB_REPO}:${DOCKER_TAG}"
                    }
                }
            }
        }

        stage("Deploy with Docker Compose") {
            steps {
                script {
                    // Shut down any running containers and remove them
                    sh "docker-compose down --volumes --remove-orphans"

                    // Bring up new containers using Docker Compose
                    sh "docker-compose up -d"
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed, check logs for errors."
        }
    }
}
