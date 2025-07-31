pipeline{
    agent any

    tools {
        maven 'myapp-maven' // name must match Global Tool config
        jdk 'myapp-jdk'      // name must match Global Tool config
    }

    environment {

        SONARQUBE_SCANNER_HOME = tool 'SonarScanner'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    slackSend(color: '#439FE0', message: ":arrow_down: Starting *Checkout* stage for `${env.JOB_NAME}` #${env.BUILD_NUMBER}")
                }

                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    slackSend(color: '#439FE0', message: ":hammer: Starting *Build* stage for `${env.JOB_NAME}` #${env.BUILD_NUMBER}")

                }

                sh 'mvn clean package'
            }
        }

         stage('Test') {
            steps {
                script {
                    slackSend(color: '#439FE0', message: ":test_tube: Starting *Test* stage for `${env.JOB_NAME}` #${env.BUILD_NUMBER}")
                }

                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    slackSend(color: '#439FE0', message: ":mag: Starting *SonarQube Analysis* for `${env.JOB_NAME}` #${env.BUILD_NUMBER}")
                }

                withSonarQubeEnv('MySonarQubeServer') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=myapp'
                }
            }
        }

        stage('Publish Artifact') {
            steps {
                script {
                    slackSend(color: '#439FE0', message: ":package: Starting *Artifact Publishing* stage for `${env.JOB_NAME}` #${env.BUILD_NUMBER}")
                }
                // Optional: Publish to internal repo if configured
                sh 'mvn deploy'
            }
        }

        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                script {
                    slackSend(color: '#36a64f', message: ":file_folder: Archived artifact for `${env.JOB_NAME}` #${env.BUILD_NUMBER}`")
                }
            }
        }
    }

    post {
        success {
            slackSend (channel: '#jenkin-pipeline', color: 'good', message: "✅ Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n${env.BUILD_URL}")
        }
        failure {
            slackSend (channel: '#jenkin-pipeline', color: 'danger', message: "❌ Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n${env.BUILD_URL}")
        }
        unstable {
            slackSend (channel: '#jenkin-pipeline', color: 'warning', message: "⚠️ Build UNSTABLE: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n${env.BUILD_URL}")
        }
    }
}