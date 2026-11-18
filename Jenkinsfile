node {

    def appName = 'jenkins-demo-app'

    stage('Application Information') {

        echo "Application: ${appName}"

        echo "Build Number: ${BUILD_NUMBER}"

        sh 'whoami'

        sh 'pwd'
    }

    stage('Build') {

        sh 'chmod +x build.sh'

        sh './build.sh'
    }

    stage('Test') {

        if (fileExists('build/index.html')) {

            echo 'HTML file exists'

        } else {

            error 'HTML file not found'
        }

        def testResult = sh(
            script: 'grep -q "Hello from GitHub and Jenkins!" build/index.html',
            returnStatus: true
        )

        if (testResult == 0) {

            echo 'Content test passed'

        } else {

            error 'Content test failed'
        }
    }

    stage('Archive Artifacts') {

        archiveArtifacts(
            artifacts: 'build/**',
            fingerprint: true
        )
    }

    echo 'Scripted Pipeline completed successfully'
}