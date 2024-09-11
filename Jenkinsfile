pipeline {
    agent any
    tools {
        maven 'Maven 3.9.9' // Or the name of your installed Maven tool
    }
    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building the project...'
                    sh 'mvn clean package'
                }
            }
        }
        stage('Unit and Integration Tests') {
            steps {
                script {
                    echo 'Running unit and integration tests...'
                    sh 'mvn test'
                }
            }
        }
        stage('Code Analysis') {
            steps {
                script {
                    echo 'Analyzing the code...'
                    sh 'mvn sonar:sonar'
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    echo 'Performing security scan...'
                    sh 'dependency-check --scan .'
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                script {
                    echo 'Deploying to staging...'
                    sh 'aws deploy create-deployment --application-name MyApp --deployment-group-name MyGroup --s3-location bucket=my-bucket,key=my-app.zip,bundleType=zip'
                }
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                script {
                    echo 'Running integration tests on staging...'
                    sh 'mvn verify'
                }
            }
        }
        stage('Deploy to Production') {
            steps {
                script {
                    echo 'Deploying to production...'
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
            script {
                logFileContent = new File("${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_NUMBER}/log").collect {it}
                mail to: 'minhhai1312004@gmail.com',
                     subject: "SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                     body: "Pipeline ${env.JOB_NAME} - Build #${env.BUILD_NUMBER} completed successfully. See attached log file.",
                     attachLog: true,
                     attachmentsPattern: logFileContent.toString()
            }
        }
        failure {
            script {
                logFileContent = new File("${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_NUMBER}/log").collect {it}
                mail to: 'minhhai1312004@gmail.com',
                     subject: "FAILURE: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                     body: "Pipeline ${env.JOB_NAME} - Build #${env.BUILD_NUMBER} failed. See attached log file for details.",
                     attachLog: true,
                     attachmentsPattern: logFileContent.toString()
            }
        }
    }
}
