
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'juice-shop'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checkout successful'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t juice-shop:${BUILD_NUMBER} .'
            }
        }

        stage('Test Application') {
            steps {
                sh '''
                    docker rm -f juice-shop-test || true

                    docker run -d \
                      --name juice-shop-test \
                      -p 3001:3000 \
                      juice-shop:${BUILD_NUMBER}

                    for i in $(seq 1 60); do
                        if curl -fsS http://localhost:3001/ > /dev/null; then
                            echo "Application test passed"
                            docker rm -f juice-shop-test
                            exit 0
                        fi
                        sleep 5
                    done

                    docker logs juice-shop-test
                    exit 1
                '''
            }
        }
    }

    post {
        always {
            sh 'docker rm -f juice-shop-test || true'
        }

        success {
            echo 'Jenkins CI pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}


