pipeline {
    agent any
    stages {
        stage('1. Configure – install dependencies (Docker, Docker Compose)') {
            steps {
                // Remember 'sudo visudo' then add 'jenkins ALL= NOPASSWD: ALL.' at the bottom
                // Steps to install Docker
                echo "Installing Docker"
                sh 'sudo apt-get update'
                sh 'sudo apt install curl -y'
                sh 'curl https://get.docker.com | sudo bash'
                sh 'sudo usermod -aG docker $(whoami)'
                sh 'su -s ${USER}'
                // Steps to install Docker-Compose
                echo "Installing Docker-Compose"
                //make sure jq & curl is installed
                sh 'sudo apt install -y curl jq'
                // set which version to download (latest)
                sh 'version=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | jq -r ".tag_name")'
                // download to /usr/local/bin/docker-compose
                sh 'sudo curl -L "https://github.com/docker/compose/releases/download/${version}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose'
                // make the file executable
                sh 'sudo chmod +x /usr/local/bin/docker-compose'
            }
        }
        stage('2. Build – build the Docker images') {
            steps {
                echo "Building images"
                sh 'docker build -t garage-backend:latest backend/.'
                sh 'docker build -t garage-frontend:latest ./frontend'
                sh 'docker build -t garage-gateway:latest ./gateway'
            }
        }
        stage('3. Push – push the Docker images') {
            steps {
                // docker username and password stored in Manage Jenkins/Manage Credentials.
                echo "Pushing Images to DockerHub"
                sh 'docker push garage-backend:latest'
                sh 'docker push garage-frontend:latest'
                sh 'docker push garage-gateway:latest'
            }
        }
        stage('4. Deploy – deploy the application') {
            steps {
                echo "Deploying App"
                sh 'docker-compose up -d'
            }
        }
    }
}