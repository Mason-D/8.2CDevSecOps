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
                bat 'npm audit || exit /b 0' // This will show known CVEs in the output
            }
        }

        //sonarcloud
        stage('SonarCloud Analysis') {
            steps {
                echo 'Starting SonarCloud Analysis...'
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    bat '''
                    REM Download SonarScanner
                    curl -o sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-windows.zip
                    
                    REM Extract SonarScanner
                    powershell -Command "Expand-Archive sonar-scanner.zip -DestinationPath ."

                    REM Set path for SonarScanner
                    set PATH=%PATH%;%CD%\sonar-scanner-4.7.0.2747-windows\bin

                    REM Inject SONAR_TOKEN into properties file
                    powershell -Command "(Get-Content sonar-project.properties).replace('\${SONAR_TOKEN}', '%SONAR_TOKEN%') | Set-Content sonar-project.properties"

                    REM Run SonarScanner using the properties file
                    sonar-scanner.bat -Dproject.settings=sonar-project.properties
                    '''
                }
            }
        }
    }
}