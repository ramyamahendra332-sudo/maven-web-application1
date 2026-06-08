pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                withMaven(maven: '#9') {   // ← Using the name you gave
                    bat 'mvn clean package -DskipTests'
                }
            }
        }
        
        stage('Test') {
            steps {
                withMaven(maven: '#9') {
                    bat 'mvn test'
                }
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        // Add SonarQube stages back if needed
    }
    
    post {
        success {
            archiveArtifacts artifacts: 'target/*.war', allowEmptyArchive: true
            echo '✅ Build Successful!'
        }
        failure {
            echo '❌ Build Failed'
        }
    }
}