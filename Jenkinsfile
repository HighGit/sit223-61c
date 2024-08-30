pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building the project...'
                    // Use Maven for build automation
                    sh 'mvn clean package'
                }
            }
        }
        stage('Unit and Integration Tests') {
            steps {
                script {
                    echo 'Running unit and integration tests...'
                    // Use JUnit for unit testing, and TestNG for integration testing
                    sh 'mvn test'
                }
            }
        }
        stage('Code Analysis') {
            steps {
                script {
                    echo 'Analyzing the code...'
                    // Use SonarQube for code analysis
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    echo 'Performing security scan...'
                    // Use OWASP Dependency-Check for security scanning
                    sh 'dependency-check --scan .'
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                script {
                    echo 'Deploying to staging...'
                    // Deploy to AWS EC2 instance (use a script or AWS CLI)
                    sh 'aws deploy create-deployment --application-name MyApp --deployment-group-name MyGroup --s3-location bucket=my-bucket,key=my-app.zip,bundleType=zip'
                }
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                script {
                    echo 'Running integration tests on staging...'
                    // Run tests using a testing framework or custom scripts
                    sh 'mvn verify'
                }
            }
        }
        stage('Deploy to Production') {
            steps {
                script {
                    echo 'Deploying to production...'
                    // Deploy to AWS EC2 instance (use a script or AWS CLI)
                    sh 'aws deploy create-deployment --application-name MyApp --deployment-group-name MyProductionGroup --s3-location bucket=my-bucket,key=my-app.zip,bundleType=zip'
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            mail to: 'minhhai1312004@gmail.com',
                 subject: "SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                 body: "Pipeline ${env.JOB_NAME} - Build #${env.BUILD_NUMBER} completed successfully."
        }
        failure {
            mail to: 'minhhai1312004@gmail.com',
                 subject: "FAILURE: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                 body: "Pipeline ${env.JOB_NAME} - Build #${env.BUILD_NUMBER} failed. Check the Jenkins logs for details."
        }
    }
}
