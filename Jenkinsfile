pipeline {
    agent any

    environment{
        NETLIFY_SITE_ID = 'ec603ab6-e6a8-4de0-a3d6-4aaa7bb13e8a'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version   
                    npm ci
                    npm run build   
                    ls -la
                    '''
            }
        }

        stage('Test'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                #Testing if index.html fiel was created in the build
                    test -f build/index.html 

                    npm test


                '''
            }
        }

        stage('Deploy') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to Production. Site Id: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                    '''
            }
        }
    }

    post{
        always{
            junit 'test-results/junit.xml'
        }
    }
}
