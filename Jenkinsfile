pipeline {
    agent any
    environment {
        packageJSONVersion = '1.0.1'  // Add your package version here
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
                pwd
                docker container run --rm \
                  -e SONAR_HOST_URL=http://54.202.174.198:9000 \
                  -e SONAR_TOKEN=sqp_40ac0b05a93d8541677784d9b3a0f782b63587a0 \
                  -v /var/lib/jenkins/workspace/lms-build/webapp:/usr/src \
                  sonarsource/sonar-scanner-cli \
                  -Dsonar.projectKey=lms \
                  -Dsonar.sources=. \
                  -Dsonar.host.url=http://54.202.174.198:9000 \
                  -Dsonar.token=sqp_40ac0b05a93d8541677784d9b3a0f782b63587a0
                '''
            }
        }

        stage('Release') {
            steps {
                echo 'Releasing..'
                // Add your release steps here
            }
        }
    }

    post {
        always {
            echo 'Cleaning up..'
            // Any cleanup steps can go here
        }
        success {
            echo 'Build succeeded.'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
