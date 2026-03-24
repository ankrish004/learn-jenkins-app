pipeline {
    agent any

    stages {
        agent{
            docker 'node:18-alpine'
            reuseNode true
        }
        stage('Build') {
            steps {
                echo 'Building...'
                sh'''    
                 ls -la
                 node --version
                 npm --version
                 npm ci
                 npm run build
                 ls -la
                '''
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
                // Add your test steps here
            }
        }

    }

}