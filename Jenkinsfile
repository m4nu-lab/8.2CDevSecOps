pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/m4nu-lab/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage || true'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        echo "Running SonarCloud analysis..."
                        uname -m

                        if [ "$(uname -m)" = "arm64" ]; then
                            SCANNER_URL="https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-8.0.1.6346-macosx-aarch64.zip"
                        else
                            SCANNER_URL="https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-8.0.1.6346-macosx-x64.zip"
                        fi

                        rm -rf sonar-scanner.zip sonar-scanner-*

curl -L "$SCANNER_URL" -o sonar-scanner.zip
unzip -q sonar-scanner.zip

SCANNER_DIR=$(find . -maxdepth 1 -type d -name "sonar-scanner-*" | head -1)
                        chmod +x "$SCANNER_DIR/bin/sonar-scanner"

                        "$SCANNER_DIR/bin/sonar-scanner"
                    '''
                }
            }
        }
    }
}
