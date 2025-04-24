pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/prerana99/django-todo.git', branch: 'develop', credentialsId: 'github-cred'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                source venv/bin/activate
                pip install -r requirements.txt
                pip install gunicorn
                '''
            }
        }
        stage('Run Migrations') {
            steps {
                sh '''
                source venv/bin/activate
                python manage.py makemigrations
                python manage.py migrate
                '''
            }
        }
        stage('Collect Static Files') {
            steps {
                sh '''
                source venv/bin/activate
                python manage.py collectstatic --noinput
                '''
            }
        }
        stage('Deploy with Gunicorn') {
            steps {
                sh '''
                sudo systemctl restart gunicorn
                sudo systemctl restart nginx
                '''
            }
        }
        stage('Verify Deployment') {
            steps {
                sh '''
                curl -f http://44.223.48.19/todos || exit 1
                '''
            }
        }
    }
    post {
        always {
            sh 'deactivate || true'
        }
        failure {
            echo 'Deployment failed!'
        }
        success {
            echo 'Deployment successful! App running at http://44.223.48.19/todos'
        }
    }
}
