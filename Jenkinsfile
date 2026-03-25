pipeline {
agent any

environment {
    NETLIFY_SITE_ID = 'da21949b-cb66-49c7-98de-14291acca67c'
    NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    
}

    stages {

        stage('Docker image build') {
            steps {
               
                sh'''
                    docker build -t playwright-netlify:latest .                  
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
                            image 'playwright-netlify:latest'
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

        stage('Staging') {
            agent {
                docker {
                    image 'playwright-netlify:latest'  
                    reuseNode true
                }
            }
            steps {
                echo 'staging...'
                sh'''

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
                    image 'playwright-netlify:latest'
                    reuseNode true
                }
            }
            
            environment {
            CI_ENVIRONMENT_URL = "${env.STAGING_URL}" 
            }

            steps {
                echo 'Deploying...'
                sh'''
                    
                    node_modules/.bin/netlify --version
                    echo "deploying to site: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --site $NETLIFY_SITE_ID --prod --dir=build
                   
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