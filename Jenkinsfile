pipeline {
    agent any

    environment {
        PROJECT_DIR = '/home/ubuntu/fullstackapp'
    }

    stages {

        stage('Check Environment') {
            steps {
                sh '''
                    echo "Checking environment..."
                    whoami
                    docker --version
                    docker compose version
                    cd $PROJECT_DIR
                    pwd
                    ls -la
                '''
            }
        }


        stage('Validate Docker Compose') {
            steps {
                sh '''
                    cd $PROJECT_DIR

                    docker compose config > /tmp/fullstackapp-compose-check.yml

                    echo "Docker Compose configuration is valid."
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    cd $PROJECT_DIR

                    docker compose build
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                    cd $PROJECT_DIR

                    docker compose up -d --remove-orphans
                '''
            }
        }

        stage('Verify Containers') {
            steps {
                sh '''
                    cd $PROJECT_DIR

                    sleep 15

                    docker compose ps

                    echo "Checking frontend..."
                    curl -I --max-time 15 http://localhost

                    echo "Checking backend..."
                    curl -I --max-time 15 http://localhost/docs || true
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }

        failure {
            echo 'Deployment failed. Check the Jenkins console log.'
        }

        always {
            sh '''
                cd $PROJECT_DIR
                docker compose ps || true
            '''
        }
    }
}
