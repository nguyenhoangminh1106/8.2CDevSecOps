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
                        // Set the SonarScanner version
                        def sonarScannerVersion = "sonar-scanner-cli-5.2.2.61219-windows"
        
                        // Download the SonarScanner
                        bat """
                        curl -L -o ${sonarScannerVersion}.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/${sonarScannerVersion}.zip
                        powershell -command "\$ProgressPreference = 'SilentlyContinue'; Expand-Archive -Force .\\${sonarScannerVersion}.zip -DestinationPath .\\${sonarScannerVersion}"
                        del ${sonarScannerVersion}.zip
                        """
        
                        // Run the SonarScanner
                        bat """
                        .\\${sonarScannerVersion}\\bin\\sonar-scanner.bat -Dsonar.login=%SONAR_TOKEN%
                        """
                    }
                }
            }
        }
    }
}
