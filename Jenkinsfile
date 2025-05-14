pipeline {
    agent any
    
    environment {
        SONAR_HOST_URL = 'http://54.202.243.49:9000'
        SONAR_LOGIN = 'sqp_7001554c780594f752f67eb89011a3d6e1d28d3e'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building..'
                sh '''
                    cd webapp
                    npm install
                    npm run build
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..'
                sh '''
                    cd webapp
                    docker container run --rm \
                        -e SONAR_HOST_URL=${SONAR_HOST_URL} \
                        -e SONAR_LOGIN=${SONAR_LOGIN} \
                        -v $(pwd):/usr/src \
                        sonarsource/sonar-scanner-cli \
                        -Dsonar.projectKey=lms
                '''
            }
        }

        stage('Release') {
            when {
                expression { return currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Releasing..'
                // Add your release steps here if needed
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build, test, and release completed successfully.'
        }
        failure {
            echo 'Build failed.'
        }
    }
}