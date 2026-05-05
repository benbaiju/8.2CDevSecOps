pipeline {
    agent any

    environment {
        EMAIL_RECIPIENT = 'ben.baiju2001@gmail.com'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Tool: Git - Pulls the latest code from the GitHub repository into the Jenkins workspace.'
                git branch: 'main', url: 'https://github.com/benbaiju/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Tool: npm - Installs all Node.js project dependencies defined in package.json.'
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Tool: Mocha + Supertest - Mocha runs the test suite and Supertest handles HTTP endpoint testing for this Express application.'
                sh 'npm test || true' // Allows pipeline to continue despite test failures
            }
            post {
                always {
                    emailext(
                        to: "${EMAIL_RECIPIENT}",
                        subject: "Jenkins | Run Tests: ${currentBuild.currentResult} | Build #${BUILD_NUMBER}",
                        body: """
                            <p><b>Stage:</b> Run Tests</p>
                            <p><b>Status:</b> ${currentBuild.currentResult}</p>
                            <p><b>Build Number:</b> #${BUILD_NUMBER}</p>
                            <p><b>Job:</b> ${JOB_NAME}</p>
                            <p>The full build log is attached.</p>
                        """,
                        mimeType: 'text/html',
                        attachLog: true
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                echo 'Tool: npm coverage script - Generates test coverage report using configured coverage tool (e.g., c8).'
                sh 'npm run coverage || true' // Ensure coverage report exists
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                echo 'Tool: npm audit - Scans project dependencies for known vulnerabilities (CVEs).'
                sh 'npm audit || true'  // This will show known CVEs in the output
            }
            post {
                always {
                    emailext(
                        to: "${EMAIL_RECIPIENT}",
                        subject: "Jenkins | Security Scan: ${currentBuild.currentResult} | Build #${BUILD_NUMBER}",
                        body: """
                            <p><b>Stage:</b> NPM Audit (Security Scan)</p>
                            <p><b>Status:</b> ${currentBuild.currentResult}</p>
                            <p><b>Build Number:</b> #${BUILD_NUMBER}</p>
                            <p><b>Job:</b> ${JOB_NAME}</p>
                            <p>The full security scan log is attached.</p>
                        """,
                        mimeType: 'text/html',
                        attachLog: true
                    )
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline complete.'
            echo 'Tool: Jenkins Email Extension Plugin - Sends automated email notifications with build status and attached logs.'
        }
    }
}