pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/sandeep-r-mishra/Online-Library-Management-System-PHP.git'
        DEPLOY_SERVER = 'username@192.168.74.129'
        DEPLOY_PATH = '/var/www/html/library'  // adjust if different
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "${REPO_URL}", branch: 'master'
            }
        }

        stage('Install Composer') {
            steps {
                sh '''
                if ! command -v composer &> /dev/null; then
                    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
                    php composer-setup.php
                    sudo mv composer.phar /usr/local/bin/composer
                fi

                if [ -f composer.json ]; then
                    composer install || true
                fi
                '''
            }
        }

        stage('Lint PHP Files') {
            steps {
                sh 'find . -name "*.php" -print0 | xargs -0 -n1 php -l'
            }
        }

        stage('Deploy to Apache Server') {
            steps {
                sh '''
                echo "Deploying to Apache at $DEPLOY_SERVER:$DEPLOY_PATH"

                ssh $DEPLOY_SERVER "sudo rm -rf $DEPLOY_PATH/*"
                rsync -avz --exclude=".git" ./ $DEPLOY_SERVER:$DEPLOY_PATH/
                ssh $DEPLOY_SERVER "sudo chown -R www-data:www-data $DEPLOY_PATH && sudo chmod -R 755 $DEPLOY_PATH"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful! Visit: http://192.168.74.129:8082/'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
