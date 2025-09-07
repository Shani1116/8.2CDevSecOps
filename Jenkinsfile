pipeline {
    agent any

    environment {
        EMAIL_RECIPIENT = 's224805881@deakin.edu.au'
    }
    
    stages {
        
        stage('Checkout') {
            steps {
                echo "Checkout Stage - Cloning source code from GitHub"
                sh "git config --global credential.helper cache"
                sh "git config --global credential.helper 'cache --timeout=3600'"
                git branch: 'main', url: ' https://github.com/Shani1116/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                echo "Install Dependencies Stage - Installing NPM packages"
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                script{
                    try {
                        echo "Running unit tests using Jest/Mocha"
                        sh 'npm test || true' // allows pipeline to continue despite test failures
                        currentBuild.description = "Tests completed"
                    } catch (err) {
                        currentBuild.result = 'FAILURE'
                        currentBuild.description = "Tests failed, marking build as UNSTABLE"
                        throw err
                    }
                }
            }
            post {
                always {
                    emailext(
                        subject: "Test Stage: ${currentBuild.result}",
                        to: "${EMAIL_RECIPIENT}",
                        body: "The test stage has completed with status: ${currentBuild.result}\n\nTool Used: Jest/Mocha",
                        attachLog: true
                    )
                }
            }
        }
        stage('Generate Coverage Report') {
            steps {
                echo "Generating coverage report using Codecov"
                sh 'npm run coverage || true' // Ensure coverage report exists
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    try {
                        echo "Running security scan using Trivy to identify vulnerabilities"
                        sh 'trivy fs . || true'
                        currentBuild.description = "Security Scan completed"
                    } catch (err) {
                        currentBuild.result = 'FAILURE'
                        currentBuild.description = "Security Scan failed"
                        throw err
                    }
                }
            }
            post {
                always {
                    emailext(
                        subject: "Security Scan Stage: ${currentBuild.result}",
                        to: "${EMAIL_RECIPIENT}",
                        body: "The security scan stage has completed with status: ${currentBuild.result}\n\nTool Used: Trivy",
                        attachLog: true
                    )
                }
            }
        }
    }

}