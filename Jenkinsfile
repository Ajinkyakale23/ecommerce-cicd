pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'JDK21'
    }

    environment {
        DEV_SERVER  = "172.31.24.123"
        TEST_SERVER = "172.31.20.201"
        PROD_SERVER = "172.31.23.214"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Dev') {
            when {
                branch 'dev'
            }
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh """
                        scp -o StrictHostKeyChecking=no target/ecommerce-cicd.war ec2-user@$DEV_SERVER:/tmp/

                        ssh -o StrictHostKeyChecking=no ec2-user@$DEV_SERVER '
                            rm -rf /opt/tomcat/webapps/ROOT
                            rm -f /opt/tomcat/webapps/ROOT.war
                            mv /tmp/ecommerce-cicd.war /opt/tomcat/webapps/ROOT.war
                        '
                    """
                }
            }
        }

        stage('Deploy to Test') {
            when {
                branch 'test'
            }
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh """
                        scp -o StrictHostKeyChecking=no target/ecommerce-cicd.war ec2-user@$TEST_SERVER:/tmp/

                        ssh -o StrictHostKeyChecking=no ec2-user@$TEST_SERVER '
                            rm -rf /opt/tomcat/webapps/ROOT
                            rm -f /opt/tomcat/webapps/ROOT.war
                            mv /tmp/ecommerce-cicd.war /opt/tomcat/webapps/ROOT.war
                        '
                    """
                }
            }
        }

        stage('Deploy to Prod') {
            when {
                branch 'prod'
            }
            steps {
                sshagent(credentials: ['ec2-ssh-key']) {
                    sh """
                        scp -o StrictHostKeyChecking=no target/ecommerce-cicd.war ec2-user@$PROD_SERVER:/tmp/

                        ssh -o StrictHostKeyChecking=no ec2-user@$PROD_SERVER '
                            rm -rf /opt/tomcat/webapps/ROOT
                            rm -f /opt/tomcat/webapps/ROOT.war
                            mv /tmp/ecommerce-cicd.war /opt/tomcat/webapps/ROOT.war
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully.'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}