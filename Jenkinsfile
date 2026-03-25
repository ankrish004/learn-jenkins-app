pipeline {
agent any

environment {
    NETLIFY_SITE_ID = 'e3589695-201e-4b3e-8170-3a6ac31a4255'
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
        stage('Staging') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'staging...'
                sh'''
                    npm install netlify-cli@20.1.1
                    npm install node-jq
                    
                    node_modules/.bin/netlify status

                    node_modules/.bin/netlify deploy --site $NETLIFY_SITE_ID  --dir=build
                    node_modules/.bin/netlify deploy --dir=build --json > deploy.json
                    
                '''
                script {
                env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy.json", returnStdout: true).trim()

               } 
            }

        }



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