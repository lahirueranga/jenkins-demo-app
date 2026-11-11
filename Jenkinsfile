
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

                stage('Parallel Tests') {

                    parallel {

                        stage('HTML File Test') {
                            steps {
                                echo 'Checking HTML file'

                                sh '''
                                    test -f build/index.html

                                    echo "HTML file exists!"
                                '''
                            }
                        }

                        stage('Content Test') {
                            steps {
                                echo 'Checking application content'

                                sh '''
                                    grep -q "Hello from GitHub and Jenkins!" build/index.html

                                    echo "Application content verified!"
                                '''
                            }
                        }

                        stage('Artifact Size Test') {
                            steps {
                                echo 'Checking artifact size'

                                sh '''
                                    test -s build/index.html

                                    echo "Artifact is not empty!"

                                    wc -c build/index.html
                                '''
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
