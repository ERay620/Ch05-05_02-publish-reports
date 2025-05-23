pipeline {
    agent {
        docker {
            image 'python:3.8-slim'
        }
    }

    options {
        buildDiscarder(logRotator(daysToKeepStr: '10', numToKeepStr: '10'))
        timeout(time: 12, unit: 'HOURS')
        timestamps()
    }

    stages {
        stage('Requirements') {
            steps {
                sh '''
                    apt-get update && apt-get install -y python3-venv
                    python3 -m venv venv
                    ./venv/bin/pip install --upgrade pip
                    ./venv/bin/pip install --requirement requirements.txt
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                    venv/bin/flake8 --ignore=E501,E231 *.py
                    venv/bin/pylint --errors-only --disable=C0301 --disable=C0326 *.py
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    venv/bin/coverage run -m pytest -v test_*.py --junitxml=pytest_junit.xml
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Build the application in this step..."
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploy the application in this step..."
            }
        }
    }

    post {
        always {
            sh 'venv/bin/coverage xml'

            junit allowEmptyResults: true, testResults: '**/pytest_junit.xml'
            junit allowEmptyResults: true, testResults: '**/pylint_junit.xml'

            publishCoverage adapters: [cobertura('**/coverage.xml')],
                sourceFileResolver: sourceFiles('STORE_LAST_BUILD')
        }
    }
}
