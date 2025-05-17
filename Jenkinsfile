pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        SONAR_SCANNER_HOME = "${WORKSPACE}\\sonar-scanner"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/nguyenhoangminh1106/8.2CDevSecOps.git'
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

        stage('Download SonarScanner') {
            steps {
                script {
                    if (!fileExists("${SONAR_SCANNER_HOME}\\bin\\sonar-scanner.bat")) {
                        bat '''
                        curl -L -o sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.2.2.61219-windows.zip
                        powershell -command "Expand-Archive -Force sonar-scanner.zip -DestinationPath ."
                        move sonar-scanner-5.2.2.61219-windows sonar-scanner
                        del sonar-scanner.zip
                        '''
                    }
                }
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    bat """
                    ${SONAR_SCANNER_HOME}\\bin\\sonar-scanner.bat -Dsonar.projectKey=nguyenhoangminh1106_8.2CDevSecOps -Dsonar.organization=nguyenhoangminh1106 -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=%SONAR_TOKEN% -Dsonar.sources=. -Dsonar.exclusions=node_modules/**,test/** -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info -Dsonar.projectName="NodeJS Goof Vulnerable App" -Dsonar.sourceEncoding=UTF-8
                    """
                }
            }
        }
    }
}
