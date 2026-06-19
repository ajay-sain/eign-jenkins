pipeline {
    agent any

    tools {
        // Must match the identifier defined in Jenkins Global Tool Configuration
        maven 'Maven'
        jdk 'jdk17'
    }

    environment {
        // Keeps build outputs organized
        APP_NAME = 'spring-boot-app'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out specific branch...'
                git branch: 'master',
                    url: 'https://github.com/ajay-sain/hello',
                    credentialsId: 'systemjenkinsuser' // ID defined in Jenkins Credentials Provider
            }
                }

        stage('Clean & Compile') {
            steps {
                echo 'Compiling the application...'
                sh 'mvn clean compile'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running JUnit tests...'
                sh 'mvn test'
            }
            post {
                always {
                    // Archives test results in Jenkins UI even if tests fail
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package & Deploy to Nexus') {
            steps {
                echo 'Packaging and uploading artifact to Nexus...'
                    
                // Pass credentials directly into Maven execution parameters
                sh "mvn clean deploy -DskipTests -Dusername=admin -Dpassword=root@123"
            }
        }
    }

    post {
        always {
            cleanWs() // Cleans the workspace to save disk space
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs above.'
        }
    }
}
