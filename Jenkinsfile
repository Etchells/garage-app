pipeline {
        environment { 
        registry = "getchells/https://hub.docker.com/repository/docker/getchells/jenkins" 
        registryCredential = 'docker' 
        dockerImage = '' 
    }
    agent any
    stages {
        stage('1. Configure – install dependencies (Docker, Docker Compose)') {
            steps {
                // Remember 'sudo visudo' then add 'jenkins ALL= NOPASSWD: ALL.' at the bottom
                // Steps to install Docker
                echo "Installing Docker"
                sh 'sudo chmod +x install_docker.sh'
                sh './install_docker.sh'
                // Steps to install Docker-Compose
                echo "Installing Docker-Compose"
                sh 'sudo chmod +x install_docker_compose.sh'
                sh './install_docker_compose.sh'
                sh 'sudo systemctl restart docker' // restarting group
            }
        }
        stage('2. Build – build the Docker images') {
            steps {
                echo "Building images"
                sh 'sudo chmod 666 /var/run/docker.sock' // not best practise but pipline fails to build without
                sh 'docker build -t garage-backend:latest ./backend'
                sh 'docker build -t garage-frontend:latest ./frontend'
                sh 'docker build -t garage-gateway:latest ./gateway'
            }
        }
        stage('3. Push – push the Docker images') {
            steps {
                //docker username and password stored in Manage Jenkins/Manage Credentials.
                echo "Pushing Images to DockerHub"
                // sh 'docker push garage-backend:latest'
                // sh 'docker push garage-frontend:latest'
                // sh 'docker push garage-gateway:latest'
            }
        }
        stage('4. Deploy – deploy the application') {
            steps {
                echo "Deploying App"
                sh 'docker-compose up --build -d'
            }
        }
    }
}