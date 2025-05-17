pipeline {
    agent any
    
    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your_github_username/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true' // Allows pipeline to continue despite test failures
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true' // Ensure coverage report exists
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true' // This will show known CVEs in the output
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                script {
                    if (!fileExists('sonar-scanner-cli')) {
                        sh '''
                            curl -L -o sonar-scanner.zip https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.2.2.61219-linux.zip
                            unzip -q sonar-scanner.zip -d ./
                            rm sonar-scanner.zip
                        '''
                    }
                    sh './sonar-scanner-cli/sonar-scanner-5.2.2.61219-linux/bin/sonar-scanner -Dsonar.login=$SONAR_TOKEN'
                }
            }
        }
    }
}
