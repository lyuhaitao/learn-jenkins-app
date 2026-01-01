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
                    if [-f node_modules]; then
                        echo "Find node_modules"
                    fi
                '''
            }
        }
        
    }
}
