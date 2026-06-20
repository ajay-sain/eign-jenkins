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
                
                // Fetch credentials securely from Jenkins Credentials Provider
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials-id', 
                                                 usernameVariable: 'NEXUS_USER', 
                                                 passwordVariable: 'NEXUS_PASS'),]) {
                    
                    // Pass credentials directly into Maven execution parameters
                    writeFile file: 'settings.xml', text: """
                    <settings>
                        <servers>
                            <server>
                                <id>nexus-snapshots</id>
                                <username>${NEXUS_USER}</username>
                                <password>${NEXUS_PASS}</password>
                            </server>
                            <server>
                                <id>nexus-releases</id>
                                <username>${NEXUS_USER}</username>
                                <password>${NEXUS_PASS}</password>
                            </server>
                        </servers>
                    </settings>
                    """
                    sh 'mvn clean deploy -DskipTests --settings settings.xml'
                }
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
