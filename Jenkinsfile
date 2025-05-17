pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
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

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    script {
                        // Download and extract SonarScanner if not present
                        if (!fileExists('sonar-scanner-cli')) {
                            bat '''
                            curl -L -o sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.2.2.61219-windows.zip
                            powershell -command "Expand-Archive -Force sonar-scanner.zip -DestinationPath sonar-scanner-cli"
                            del sonar-scanner.zip
                            '''
                        }

                        // Run SonarScanner
                        bat '''
                        sonar-scanner-cli\\sonar-scanner-5.2.2.61219-windows\\bin\\sonar-scanner.bat -Dsonar.login=%SONAR_TOKEN%
                        '''
                    }
                }
            }
        }
    }
}
