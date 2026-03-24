pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            reuseNode true
        }
    }

    stages {
        /*
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
        }*/
        stage('Test') {

            steps {
                echo 'Testing...'
                sh'''
                    test -f build/index.html
                    npm test
                '''
            }
        }
        stage('playright test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.58.2-noble'
                    reuseNode true
                }
            }
            steps {
                sh'''
                    npm install -g
                    serve -s build 
                    npx playwright test
                ''' 

            }
        }

    }
    post {
        always {
            junit 'test-results/*.xml'
            
            
        }
    }

}