pipeline {
    agent any  // Uses the default Jenkins node (no Docker)

    environment {
        REPO_URL = 'https://github.com/sandeep-r-mishra/Online-Library-Management-System-PHP.git'
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
                if ! [ -x "$(command -v composer)" ]; then
                    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
                    php composer-setup.php
                    sudo mv composer.phar /usr/local/bin/composer
                fi
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
    }

    post {
        success {
            echo '✅ Build and test successful!'
        }
        failure {
            echo '❌ Build or test failed.'
        }
    }
}
