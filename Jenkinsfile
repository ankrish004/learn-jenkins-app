pipeline {
agent any

environment {
    NETLIFY_SITE_ID = '5ff15c07-9220-437d-a7bc-508c4186a75f'
    NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    
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
        }
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
                        echo 'Testing build'
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
        */
 

        stage('Deploy porduction') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            
            environment {
            CI_ENVIRONMENT_URL = "${env.STAGING_URL}" 
            }

            steps {
                echo 'Deploying...'
                sh'''
                    node --version
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo "deploying to site: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --site $NETLIFY_SITE_ID --prod --dir=build
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
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