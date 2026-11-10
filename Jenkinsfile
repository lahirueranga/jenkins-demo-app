
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code'
                checkout scm
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
                echo 'Testing application'

                sh '''
                    test -f build/index.html
                    grep -q "Hello from GitHub and Jenkins!" build/index.html

                    echo "All tests passed!"
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
            echo 'CI Pipeline completed successfully!'
        }

        failure {
            echo 'CI Pipeline failed!'
        }
    }
}
