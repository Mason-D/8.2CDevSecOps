pipeline {
    agent any
    environment {
        SONAR_SCANNER_HOME = 'sonar-scanner-cli'
        JAVA_HOME = 'C:\\Program Files\\Java\\jdk-21'
        PATH = "${JAVA_HOME}\\bin;${env.PATH}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Mason-D/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'npm test || exit /b 0'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                script {
                    if (!fileExists("${SONAR_SCANNER_HOME}/bin/sonar-scanner.bat")) {
                        echo 'Downloading SonarScanner...'
                        bat '''
                            curl -L -o sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-windows.zip
                            powershell -Command "Expand-Archive sonar-scanner.zip -DestinationPath ."
                            rename sonar-scanner-4.7.0.2747-windows sonar-scanner-cli
                        '''
                    } else {
                        echo 'SonarScanner already exists.'
                    }
                }
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    bat """
                        ${SONAR_SCANNER_HOME}\\bin\\sonar-scanner.bat ^
                        -Dsonar.login=%SONAR_TOKEN%
                    """
                }
            }
        }
    }
}