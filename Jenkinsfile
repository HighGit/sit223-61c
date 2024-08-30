pipeline {
    agent any

    environment {
        MAVEN_HOME = tool 'Maven'  // Assumes Maven is installed on Jenkins
        SONARQUBE_URL = 'http://your-sonarqube-server:9000'
        SONARQUBE_TOKEN = credentials('sonarqube-token')  // Assumes you have set up SonarQube token credentials
    }

    stages {
        stage('Build') {
            steps {
                script {
                    // Maven build
                    withEnv(["PATH+MAVEN=${MAVEN_HOME}/bin"]) {
                        sh 'mvn clean package'
                    }
                }
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                script {
                    // Run unit and integration tests
                    withEnv(["PATH+MAVEN=${MAVEN_HOME}/bin"]) {
                        sh 'mvn test'
                    }
                }
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml' // Publish test results
                }
            }
        }

        stage('Code Analysis') {
            steps {
                script {
                    // SonarQube analysis
                    withEnv(["PATH+MAVEN=${MAVEN_HOME}/bin"]) {
                        sh "mvn sonar:sonar -Dsonar.host.url=${SONARQUBE_URL} -Dsonar.login=${SONARQUBE_TOKEN}"
                    }
                }
            }
        }

        stage('Security Scan') {
            steps {
                script {
                    // OWASP Dependency-Check scan
                    sh 'mvn dependency-check:check'
                }
            }
            post {
                always {
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    // Deployment to staging using AWS CLI (replace with your deployment method)
                    sh '''
                        aws s3 cp target/myapp.war s3://my-staging-bucket/myapp.war
                        aws elasticbeanstalk create-application-version --application-name myapp --version-label staging --source-bundle S3Bucket=my-staging-bucket,S3Key=myapp.war
                        aws elasticbeanstalk update-environment --application-name myapp --environment-name myapp-staging --version-label staging
                    '''
                }
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                script {
                    // Integration tests on staging
                    sh 'mvn verify -Denv=staging'
                }
            }
            post {
                always {
                    junit '**/target/failsafe-reports/*.xml' // Publish test results
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                script {
                    // Deployment to production using AWS CLI (replace with your deployment method)
                    sh '''
                        aws s3 cp target/myapp.war s3://my-production-bucket/myapp.war
                        aws elasticbeanstalk create-application-version --application-name myapp --version-label production --source-bundle S3Bucket=my-production-bucket,S3Key=myapp.war
                        aws elasticbeanstalk update-environment --application-name myapp --environment-name myapp-production --version-label production
                    '''
                }
            }
        }
    }

    post {
        success {
            mail to: 'team@example.com',
                 subject: "Pipeline Success: ${currentBuild.fullDisplayName}",
                 body: "The Jenkins pipeline succeeded. Check the results at ${env.BUILD_URL}."
        }
        failure {
            mail to: 'team@example.com',
                 subject: "Pipeline Failure: ${currentBuild.fullDisplayName}",
                 body: "The Jenkins pipeline failed. Check the results at ${env.BUILD_URL}."
        }
    }
}
