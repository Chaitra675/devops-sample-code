
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                sh '''
    rm -rf .venv
    python3 -m venv .venv
    .venv/bin/pip install --no-cache-dir -r requirements.txt
    .venv/bin/pip show Flask Werkzeug
'''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh '.venv/bin/python -m unittest discover -s .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                    mkdir -p "${WORKSPACE}/python-app-deploy"
                    cp app.py "${WORKSPACE}/python-app-deploy/"
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Starting Flask application...'
                sh '''
                    nohup "${WORKSPACE}/.venv/bin/python" \
                        "${WORKSPACE}/python-app-deploy/app.py" \
                        > "${WORKSPACE}/python-app-deploy/app.log" 2>&1 &
                    echo $! > "${WORKSPACE}/python-app-deploy/app.pid"
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing Flask application...'
                sh '.venv/bin/python test_app.py'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
    }
}
