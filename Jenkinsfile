pipeline {

    agent any

    environment {
        APP_NAME = 'jenkins-demo-app'
    }

    stages {

        stage('Application Information') {
            steps {
                echo "Application: ${APP_NAME}"
                echo "Build Number: ${BUILD_NUMBER}"
                sh 'whoami'
                sh 'pwd'
            }
        }

        stage('Build') {
            steps {
                
                        sh '''
                            chmod +x build.sh
                            ./build.sh
                        '''
            }
        }

        stage('Test') {
            steps {
                sh 'test -f build/index.html'
                sh 'grep -q "Hello from GitHub and Jenkins!" build/index.html'

                echo 'All tests passed!'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'build/**',
                                 fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Declarative Pipeline successful!'
        }

        failure {
            echo 'Declarative Pipeline failed!'
        }

        always {
            echo 'Declarative Pipeline finished!'
        }
    }
}