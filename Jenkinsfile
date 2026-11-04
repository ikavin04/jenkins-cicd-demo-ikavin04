pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checked out'
            }
        }

        stage('Build') {
            steps {
                powershell '''
                    New-Item -ItemType Directory -Force -Path build | Out-Null
                    Copy-Item -Path index.html -Destination build/index.html -Force
                '''
                echo 'Build completed'
            }
        }

        stage('Test') {
            steps {
                powershell '''
                    if (!(Test-Path "build/index.html")) {
                        throw "Application file not found"
                    }

                    $content = Get-Content "build/index.html" -Raw

                    if ($content -notmatch "Welcome to Jenkins CI/CD Pipeline") {
                        throw "Required heading not found"
                    }

                    Write-Output "All tests passed"
                '''
            }
        }

        stage('Package') {
            steps {
                powershell '''
                    Compress-Archive -Path build/* -DestinationPath app-package.zip -Force
                '''
                echo 'Application packaged successfully'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'app-package.zip'
            echo 'Artifact archived successfully'
        }

        failure {
            echo 'Pipeline failed. Artifact not archived.'
        }
    }
}