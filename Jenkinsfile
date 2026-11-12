
pipeline {

    agent none

    environment {
        APP_NAME = 'jenkins-demo-app'
        BUILD_VERSION = '1.0'
    }

    stages {

        stage('Checkout') {

            agent any

            steps {
                echo 'Checking out source code'
                checkout scm
            }
        }

        stage('Application Information') {

            agent any

            steps {
                echo "Application: ${APP_NAME}"
                echo "Version: ${BUILD_VERSION}"

                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Pipeline Information') {

            agent any

            steps {
                echo "Build Number: ${BUILD_NUMBER}"
                echo "Job Name: ${JOB_NAME}"
                echo "Workspace: ${WORKSPACE}"
                echo "Build URL: ${BUILD_URL}"
            }
        }

        stage('Agent Information') {

            agent any

            steps {
                echo "Node Name: ${NODE_NAME}"
                echo "Executor Number: ${EXECUTOR_NUMBER}"

                sh 'uname -a'
                sh 'whoami'
                sh 'pwd'
            }
        }

        stage('Build') {

            agent any

            steps {
                echo 'Building application'

                sh '''
                    chmod +x build.sh
                    ./build.sh
                '''
            }
        }

        stage('Test') {

            agent any

            steps {
                echo 'Running application tests'

                sh '''
                    test -f build/index.html

                    grep -q "Hello from GitHub and Jenkins!" build/index.html

                    echo "Tests passed!"
                '''
            }
        }

        stage('Archive Artifacts') {

            agent any

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
