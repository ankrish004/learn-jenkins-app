pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
        }
    }

    stages {

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
                sh'''
                    test -f build/index.html
                    npm test
                '''
            }
        }

    }

}