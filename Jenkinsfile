pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                agent {
                    docker {
                        image 'node:18-alpine'
                        reuse node true
                    }
                }

            steps {
                sh '''
                 ls-la
                 node --version
                 npm --version
                 npm ci
                 npm run build
                 ls -la

                '''
                }    
            }
        }
    }
}
