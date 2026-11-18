
pipeline {

    agent none

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

        stage('Application CI') {

            agent {
                label 'linux-lab'
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

                stage('Credentials Test') {
                    steps {
                        echo 'Testing Jenkins credentials integration'
                        withCredentials([
                            string(
                                credentialsId: 'learning-secret',
                                variable: 'MY_SECRET'
                                )
                                ]) {
                            sh '''
                                if [ -n "$MY_SECRET" ]; then
                                    echo "SUCCESS: Jenkins credential is available"
                                else
                                    echo "FAILURE: Jenkins credential is empty"
                                    exit 1
                                fi
                            '''
                        }
                    }
                }
stage('Username Password Credentials Test') {

    steps {

        echo 'Testing username/password credentials'

        withCredentials([
            usernamePassword(
                credentialsId: 'learning-userpass',
                usernameVariable: 'TEST_USERNAME',
                passwordVariable: 'TEST_PASSWORD'
            )
        ]) {

            sh '''
                if [ -n "$TEST_USERNAME" ] &&
                   [ -n "$TEST_PASSWORD" ]; then

                    echo "SUCCESS: Username is available"
                    echo "SUCCESS: Password is available"

                else

                    echo "FAILURE: Credentials are missing"
                    exit 1

                fi
            '''

        }
    }
}
                stage('Pipeline Information') {
                    steps {
                        echo "Build Number: ${BUILD_NUMBER}"
                        echo "Job Name: ${JOB_NAME}"
                        echo "Workspace: ${WORKSPACE}"
                        echo "Build URL: ${BUILD_URL}"
                        }
                }

                stage('Agent Information') {
                    steps {
                        echo "Node Name: ${NODE_NAME}"
                        echo "Executor Number: ${EXECUTOR_NUMBER}"
                        
                        sh 'uname -a'
                        sh 'whoami'
                        sh 'pwd'
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
                            whoami
                            pwd
                            df -h
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
                
                stage('Parallel Tests') {
                    parallel {

                        stage('HTML File Test') {
                            steps {
                                sh 'test -f build/index.html'
                                echo 'HTML file exists!'
                            }
                        }

                        stage('Content Test') {
                            steps {
                                sh '''
                                    grep -q "Hello from GitHub and Jenkins!" build/index.html
                                '''
                                echo 'Application content verified!'
                            }
                        }

                        stage('Artifact Size Test') {
                            steps {
                                sh '''
                                    test -s build/index.html
                                    wc -c build/index.html
                                '''
                                echo 'Artifact is not empty!'
                            }
                        }
                    }
                }

                stage('Optional Quality Check') {
                    steps {

                        catchError(
                            buildResult: 'UNSTABLE',
                            stageResult: 'FAILURE'
                        ) {

                            echo 'Running optional quality check'

                            sh 'exit 1'
                        }
                    }
                }

                stage('Recovery and Reliability') {

                    steps {

                        script {

                            echo 'Starting error handling demonstration'

                            try {

                                echo 'Executing an operation'

                                sh 'exit 1'

                            } catch (Exception e) {

                                echo "Caught exception: ${e.getMessage()}"

                            }

                            try {

                                error 'Demonstration error'

                            } catch (Exception e) {

                                echo "Handled Jenkins error: ${e.getMessage()}"

                            }
                        }

                        retry(3) {

                            echo 'Executing retry demonstration'

                            sh 'echo "Retry operation completed successfully"'

                        }

                        timeout(time: 10, unit: 'SECONDS') {

                            echo 'Starting timed operation'

                            sh 'sleep 3'

                            echo 'Timed operation completed'

                        }
                    }
                }

                stage('Archive Artifacts') {
                    steps {
                        archiveArtifacts artifacts: 'build/**',
                                         fingerprint: true
                    }
                }
            }
        }
    }

    post {

    success {
        echo 'SUCCESS: Pipeline completed!'
    }

    failure {
        echo 'FAILURE: Pipeline failed!'
    }

    always {
        echo 'Pipeline execution finished.'

        
        node('linux-lab') {
            echo 'Cleaning Jenkins workspace...'

            cleanWs()

            echo 'Workspace cleanup completed!'
        }
    }
}
}
