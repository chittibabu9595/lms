pipeline {
    agent any

    environment {
        // Binds Jenkins credentials (type: Username with password)
        NEXUS_CRED = credentials('nexus')
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'cd webapp && npm install && npm run build'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
                sh '''
                    cd webapp
                    sudo docker container run --rm \
                        -e SONAR_HOST_URL="http://54.202.243.49:9000" \
                        -e SONAR_LOGIN="sqp_7001554c780594f752f67eb89011a3d6e1d28d3e" \
                        -v "$(pwd):/usr/src" \
                        sonarsource/sonar-scanner-cli \
                        -Dsonar.projectKey=lms
                '''
            }
        }

        stage('Release') {
            steps {
                echo 'Releasing to Nexus...'
                sh '''
                    cd webapp
                    rm -f dist-*.zip
                    zip -r dist-${BUILD_NUMBER}.zip dist
                    curl -v -u $NEXUS_CRED_USR:$NEXUS_CRED_PSW \
                        --upload-file dist-${BUILD_NUMBER}.zip \
                        http://54.202.243.49:8081/repository/lms/
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
