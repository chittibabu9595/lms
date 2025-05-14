pipeline {
    agent any

    environment {
        NEXUS_CRED = credentials('nexus')
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh '''
                    cd webapp
                    npm install
                    npm run build
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
                sh '''
                    cd webapp
                    docker run --rm \
                        -e SONAR_HOST_URL=http://20.172.187.108:9000 \
                        -e SONAR_LOGIN=sqp_cae41e62e13793ff17d58483fb6fb82602fe2b48 \
                        -v "$PWD:/usr/src" \
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
                    zip -r dist-${BUILD_NUMBER}.zip dist
                    curl -u $NEXUS_CRED_USR:$NEXUS_CRED_PSW \
                        --upload-file dist-${BUILD_NUMBER}.zip \
                        http://54.202.243.49:8081/repository/lms/
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully.'
        }
        failure {
            echo '❌ Pipeline failed. Please check logs.'
        }
    }
}
