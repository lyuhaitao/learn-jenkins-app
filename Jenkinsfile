pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            args '-v /var/lib/jenkins/.npm:/.npm'
            reuseNode true
        }
    }

    stages {
        stage('Clean npm') {
            steps {
                sh '''
                    ls -la
                    if [ -d "node_modules" ]; then
                        echo "node_modules directory exists"
                        rm -rf node_modules
                        rm -rf package-lock.json
                        npm cache clean --force
                    else
                        echo "node_modules directory NOT found"
                    fi
                '''
            }
        }
        stage('Install dependencies') {
            steps {
                echo "Start to install dependencies."
                sh '''
                    ls -la
                    node --version
                    npm -v
                    npm config get cache
                '''
                
            }
        }
    }
}
