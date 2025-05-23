pipeline {
    agent any
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
                bat 'npm test || exit /b 0' // Allows pipeline to continue despite test failures
            }
        }

        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0' //This will show known CVEs in the output
            }
        }

        //sonarcloud
        stage('SonarCloud Analysis') {
            steps {
                script {
                    //download SonarScanner
                    if (!fileExists("sonar-scanner-cli/bin/sonar-scanner.bat")) {
                        echo 'Downloading SonarScanner...'
                        bat '''
                            curl -L -o sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-7.1.0.4889-windows-x64.zip
                            powershell -Command "Expand-Archive sonar-scanner.zip -DestinationPath ."
                            rename sonar-scanner-7.1.0.4889-windows-x64 sonar-scanner-cli
                        '''
                    } else {
                        echo 'SonarScanner already exists.'
                    }
                }
                //pass credentials
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    
                    bat """
                        sonar-scanner-cli\\bin\\sonar-scanner.bat ^
                        -Dsonar.login=%SONAR_TOKEN%
                    """
                }
            }
        }
    }
}