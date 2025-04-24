pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git url: 'git@github.com:prerana99/django-todo.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                sh '''
                source /home/ubuntu/django-todo/venv/bin/activate
                python manage.py makemigrations
                python manage.py migrate
                python manage.py collectstatic --noinput
                '''
            }
        }
        stage('Test') {
            steps {
                sh '''
                source /home/ubuntu/django-todo/venv/bin/activate
                python manage.py test
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh '''
                source /home/ubuntu/django-todo/venv/bin/activate
                pkill -f gunicorn || true  # Stop any running Gunicorn instance
                gunicorn --workers 3 --bind 0.0.0.0:8000 todoApp.wsgi:application &
                sleep 5  # Wait for server to start
                '''
            }
        }
        stage('Verify Deployment') {
            steps {
                sh '''
                echo "Checking deployment at http://44.223.48.19:8000/todos"
                curl -f http://44.223.48.19:8000/todos || exit 1
                echo "Deployment check passed"
                '''
            }
        }
    }
    post {
        always {
            sh 'deactivate || true'  // Deactivate virtualenv if active
        }
        failure {
            echo 'Deployment failed! Check logs.'
        }
        success {
            echo 'Deployment successful! App running at http://44.223.48.19:8000/todos'
        }
    }
}
