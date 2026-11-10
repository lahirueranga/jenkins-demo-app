
pipeline {

    agent any

    environment {
        APP_NAME = 'jenkins-demo-app'
        BUILD_VERSION = '1.0'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code'
                checkout scm
            }
        }

        stage('Application Information') {
            steps {
                echo "Application: ${APP_NAME}"
                echo "Version: ${BUILD_VERSION}"

                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application'

                sh '''
                    chmod +x build.sh
                    ./build.sh
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests'

                sh '''
                    test -f build/index.html
                    grep -q "Hello from GitHub and Jenkins!" build/index.html

                    echo "Tests passed!"
                '''
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
            echo 'SUCCESS: Application pipeline completed!'
        }

        failure {
            echo 'FAILURE: Application pipeline failed!'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
