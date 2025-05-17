pipeline {
    agent any
    
    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                bat 'git clone https://github.com/nguyenhoangminh1106/8.2CDevSecOps.git .'
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
                bat 'npm run coverage || exit /b 0' // Ensure coverage report exists
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0' // This will show known CVEs in the output
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                script {
                    if (!fileExists('sonar-scanner-cli')) {
                        bat '''
                            curl -L -o sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.2.2.61219-windows.zip
                            powershell -Command "Expand-Archive sonar-scanner.zip -DestinationPath ."
                            del sonar-scanner.zip
                        '''
                    }
                    bat '.\\sonar-scanner-cli\\sonar-scanner-5.2.2.61219-windows\\bin\\sonar-scanner -Dsonar.login=%SONAR_TOKEN%'
                }
            }
        }
    }
}
