pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        SONAR_SCANNER_VERSION = '5.0.1.3006'
        SONAR_SCANNER_HOME = "${WORKSPACE}\\sonar-scanner-${SONAR_SCANNER_VERSION}-windows"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/s225631392/8.2CDevSecOps.git'
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
                bat '''
                    if not exist "sonar-scanner-%SONAR_SCANNER_VERSION%-windows" (
                        echo Downloading SonarScanner...
                        curl -sSLo sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-%SONAR_SCANNER_VERSION%-windows.zip
                        echo Extracting SonarScanner...
                        tar -xf sonar-scanner.zip
                    )
                    "%SONAR_SCANNER_HOME%\\bin\\sonar-scanner.bat" -Dsonar.login=%SONAR_TOKEN%
                '''
            }
        }
    }
}
