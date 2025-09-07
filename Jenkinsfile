pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                sh "git config --global credential.helper cache"
                sh "git config --global credential.helper 'cache --timeout=3600'"
                git branch: 'main', url: ' https://github.com/Shani1116/8.2CDevSecOps.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test || true' // allows pipeline to continue despite test failures
            }
        }
        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true' // Ensure coverage report exists
            }
        }
        stage('NPM Audit') {
            steps {
                sh 'npm audit || true' // This will show known CVEs in the output
            }
        }
    }

}