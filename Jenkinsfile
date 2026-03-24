pipeline {
agent any

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
        stage ('Build and Test') {
            parallel {
                stage('Test') {
                agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

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
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        
                        }
                    }
                    steps {
                        sh'''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        ''' 

                    }
                }

            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'Deploying...'
                sh'''
                    npm install netlify-cli -g
                    netlify --version
                '''
            }
        }


    }
    post {
        always {
            junit 'Jtest-results/*.xml'
            
            
        }
    }

}