@Library('jenkins-library') _
pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'sonarqubeserver' // Name of SonarQube server configured in Jenkins (Manage Jenkins > Configure System)
        NODE_HOME = "${tool 'NodeJsTool'}"   // Replace 'NodeJsTool' with the NodeJS tool name configured in Jenkins
        PATH = "C:\\Users\\devop\\AppData\\Roaming\\npm;${env.PATH}"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
                echo 'Workspace cleaned successfully!'
            }
        }

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/devops81/angular-realworld-example-app.git'
            }
        }

        stage('Deploy1') {
            steps {
                deployApp('angular-realworld-example-app')
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    env.PATH = "${NODE_HOME}\\bin;${env.PATH}"
                    bat 'npm install core-js@latest'
                    bat 'npm cache clean --force'
                    bat 'npm install --legacy-peer-deps'
                    bat 'npm install -g @angular/cli'
                    bat 'ng build'
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Wrap SonarQube environment
                    withSonarQubeEnv('sonarqubeserver') {
                        bat """
                            ${env.NODE_HOME}\\bin\\npx sonar-scanner ^
                            -Dsonar.projectKey=angular-app-sonarqube ^
                            -Dsonar.sources=. ^
                            -Dsonar.host.url=http://localhost:9000 ^
                            -Dsonar.login=${env.SONARQUBE_TOKEN}
                        """
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
            }
        }

        stage('Checkout Angular App') {
            steps {
                checkoutAngularApp('https://github.com/devops81/angular-app-sonarqube.git', 'C:/newcheckout')
                echo "it's ok"
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
