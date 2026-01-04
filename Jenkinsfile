pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '8d9f72a9-2714-4c1a-a6ae-d33704516c81'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-v /var/lib/jenkins/.npm:/.npm'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    if [ -d "node_modules" ]; then
                        echo "node_modules directory exists - Remove old node-modules"
                        rm -rf node_modules
                        npm cache clean --force
                    else
                        echo "node_modules directory NOT found"
                    fi
                    echo "Start to install dependencies."
                    node --version
                    npm -v
                    npm config get cache
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        
        stage('Test Stage') {
            parallel {
                stage ('Unit tests'){
                    agent {
                        docker {
                            image 'node:18-alpine'
                            args '-v /var/lib/jenkins/.npm:/.npm'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            #test -f build/index.html
                            npm test
                        '''
                    }
                    post {
                        always {
                            junit 'test-results/junit.xml'
                        }
                    }

                }
                stage ('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            args '-v /var/lib/jenkins/.npm:/.npm'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test  --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }

                }
            }
            
        }
        
        stage ('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine-jenkins'
                    
                    reuseNode true
                }
            }
            
            steps {
                sh '''
                    ls -la
                    echo $WORKSPACE
                    npm install -g netlify-cli
                    ls -ld /usr/local/bin
                    netlify -v
                    #ls -ld node_modules/.bin/netlify
                    #node_modules/.bin/netlify -v
                    netlify status
                    netlify deploy --dir=build --prod
                '''
            }

        }
    }
}
