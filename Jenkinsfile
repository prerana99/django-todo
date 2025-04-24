pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/prerana99/django-todo.git', branch: 'main', credentialsId: 'github-cred'
            }
        }
        stage('Setup Virtual Environment') {
            steps {
                sh '''
                #!/bin/bash
                python3 -m venv venv
                chmod -R 755 venv
                . venv/bin/activate
                which pip
                pip --version
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                #!/bin/bash
                . venv/bin/activate
                pip install --no-cache-dir -r requirements.txt
                pip install gunicorn
                '''
            }
        }
        stage('Run Migrations') {
            steps {
                sh '''
                #!/bin/bash
                . venv/bin/activate
                python manage.py makemigrations
                python manage.py migrate
                '''
            }
        }
        stage('Collect Static Files') {
            steps {
                sh '''
                #!/bin/bash
                . venv/bin/activate
                python manage.py collectstatic --noinput
                '''
            }
        }
        stage('Deploy with Gunicorn') {
            steps {
                sh '''
                #!/bin/bash
                sudo systemctl restart gunicorn
                sudo systemctl restart nginx
                '''
            }
        }
        stage('Verify Deployment') {
            steps {
                sh '''
                #!/bin/bash
                curl -f http://44.223.48.19/todos || exit 1
                '''
            }
        }
    }
    post {
        always {
            sh '''
            #!/bin/bash
            deactivate || true
            '''
        }
        failure {
            echo 'Deployment failed!'
        }
        success {
            echo 'Deployment successful! App running at http://44.223.48.19/todos'
        }
    }
}
