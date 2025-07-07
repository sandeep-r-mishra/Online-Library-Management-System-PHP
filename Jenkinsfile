
pipeline {
    agent {
        docker {
            image 'php:8.2-cli' // or 'composer:latest'
        }
    }

    environment {
        REPO_URL = 'https://github.com/sandeep-r-mishra/Online-Library-Management-System-PHP.git'
        DEPLOY_SERVER = 'http://localhost:8082/'
        DEPLOY_PATH = '/var/www/html/library' // Change based on your structure
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
                php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
                php composer-setup.php
                mv composer.phar /usr/local/bin/composer
                composer install || true
                '''
            }
        }

        stage('Lint PHP Files') {
            steps {
                sh '''
                find . -name "*.php" -print0 | xargs -0 -n1 php -l
                '''
            }
        }

        stage('Run PHPUnit Tests') {
            when {
                expression { fileExists('vendor/bin/phpunit') }
            }
            steps {
                sh './vendor/bin/phpunit'
            }
        }

        stage('Deploy to Apache Server') {
            steps {
                sh '''
                echo "Deploying application to Apache server..."

                # Clean old deployment
                ssh $DEPLOY_SERVER "rm -rf $DEPLOY_PATH/*"

                # Copy project files to Apache web root
                rsync -avz --exclude=".git" ./ $DEPLOY_SERVER:$DEPLOY_PATH/

                # (Optional) Set proper permissions
                ssh $DEPLOY_SERVER "chown -R www-data:www-data $DEPLOY_PATH && chmod -R 755 $DEPLOY_PATH"
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build, test and deployment successful!'
        }
        failure {
            echo '❌ Build, test or deployment failed.'
        }
    }
}
