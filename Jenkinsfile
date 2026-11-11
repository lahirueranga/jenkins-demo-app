
pipeline {

    agent any

    environment {
        APP_NAME = 'jenkins-demo-app'
        BUILD_VERSION = '1.0'
    }

    parameters {
        choice(
            name: 'BUILD_MODE',
            choices: ['NORMAL', 'DIAGNOSTIC'],
            description: 'Select application build mode'
        )
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
                echo "Build Mode: ${params.BUILD_MODE}"

                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Build Information') {
            when {
                expression {
                    params.BUILD_MODE == 'DIAGNOSTIC'
                }
            }

            steps {
                echo 'Running diagnostic information'

                sh '''
                    echo "Current user:"
                    whoami

                    echo "Current directory:"
                    pwd

                    echo "Available disk space:"
                    df -h

                    echo "Workspace files:"
                    ls -la
                '''
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
