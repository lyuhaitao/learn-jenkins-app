pipeline {
    agent {
        docker {
            image 'node:18-alpine'
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
                    else
                        echo "node_modules directory NOT found"
                    fi
                '''
            }
        }
    }
}
