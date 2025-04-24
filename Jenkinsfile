pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // GitHub repo clone karo
                git url: 'git@github.com:prerana99/django-todo.git', branch: 'main'
            }
        }
        stage('Build') {
            steps {
                // Virtualenv activate aur dependencies setup
                sh '''
                source /home/ubuntu/django-todo/venv/bin/activate
                pip install -r requirements.txt
                python manage.py makemigrations
                python manage.py migrate
                python manage.py collectstatic --noinput
                '''
            }
        }
        stage('Test') {
            steps {
                // Django tests run karo
                sh '''
                source /home/ubuntu/django-todo/venv/bin/activate
                python manage.py test
                '''
            }
        }
        stage('Deploy') {
            steps {
                // Gunicorn start karo port 8000 pe
                sh '''
                source /home/ubuntu/django-todo/venv/bin/activate
                pkill -f gunicorn || true  # Agar pehle se chal raha ho toh band karo
                gunicorn --workers 3 --bind 0.0.0.0:8000 todoApp.wsgi:application &
                sleep 5  # Server start hone ka wait
                '''
            }
        }
        stage('Verify Deployment') {
            steps {
                // Deployment check karo
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
            sh 'deactivate || true'  // Virtualenv deactivate
        }
        failure {
            echo 'Pipeline failed! Check logs.'
        }
        success {
            echo 'Pipeline successful! App running at http://44.223.48.19:8000/todos'
        }
    }
}
