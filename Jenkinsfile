pipeline {
    agent {
        docker {
            image 'python:3.10'
        }
    }

    environment {
        VENV_DIR = 'env'
    }

    stages {
        stage('Setup Virtualenv') {
            steps {
                sh '''
                    python -m venv $VENV_DIR
                    . $VENV_DIR/bin/activate
                    pip install --upgrade pip
                    pip install --no-cache-dir -r requirements.txt
                '''
            }
        }
        stage('Run Tests') {
            steps {
                sh '''
                    . $VENV_DIR/bin/activate
                    pytest test_app.py
                '''
            }
        }
        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: 'release/.*', comparator: 'REGEXP'
                }
            }
            steps {
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on ${env.BRANCH_NAME}\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1371372122705756261/HghC1K0GG86E4az6cglMEr78BxcpBh7RZxWAnQII_udVAMVdyLpx0EPJPax0fHw0oC_E'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on ${env.BRANCH_NAME}\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1371372122705756261/HghC1K0GG86E4az6cglMEr78BxcpBh7RZxWAnQII_udVAMVdyLpx0EPJPax0fHw0oC_E'
                )
            }
        }
    }
}
