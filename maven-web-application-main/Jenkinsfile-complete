pipeline {
    // Jenkins CI/CD pipeline for maven-web-application - all 4 stages as per docx

    agent any

    tools {
        maven 'maven3.8.2'
    }

    triggers {
        pollSCM('* * * * *')
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
    }

    stages {

        stage('CheckOutCode') {
            steps {
                git branch: 'main', url: 'https://github.com/jeevan0024/maven-web-application.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn clean sonar:sonar -Dsonar.login=admin -Dsonar.password=Jeevan0024'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        /*
        stage('Upload To Nexus') {
            steps {
                sh 'mvn clean deploy'
            }
        }
        */

        stage('Deploy via Ansible') {
            steps {
                sh 'ansible-playbook -i inventory appdeploy.yaml'
            }
        }

    }

    post {
        success {
            emailext to: 'admin@example.com',
                subject: "Build #${env.BUILD_NUMBER} SUCCESS",
                body: "Pipeline completed successfully. Build: ${env.BUILD_NUMBER}"
        }
        failure {
            emailext to: 'admin@example.com',
                subject: "Build #${env.BUILD_NUMBER} FAILED",
                body: "Pipeline failed at stage. Check Jenkins logs. Build: ${env.BUILD_NUMBER}"
        }
    }

}
