pipeline {
    agent any

    tools {
        maven 'maven_3_5_0'
    }

    environment {
        DATE = new Date().format('yy.M')
        name = 'UAT'
    }

    stages {
        stage('Git Clone') {
            steps {
                git branch: 'main', credentialsId: 'Github', url: 'https://github.com/ankit290793/demo_project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    if (isUATEnvironment()) {
                        // UAT-specific test cases
                        sh 'run-uat-tests.sh'
                    } else {
                        sh 'mvn test'
                    }
                }
            }
        }

        stage('Publish Test Results') {
            steps {
                junit 'target/surefire-reports/*.xml'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ankit290793/devops-project:latest .'
            }
        }

        stage('Push Image to Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                    sh "docker login -u ankit290793 -p ${dockerhubpwd}"
                }
                sh 'docker push ankit290793/devops-project:latest'
            }
        }

        stage('Send Email') {
            steps {
                emailext subject: 'UAT Test Results',
                          body: 'This is the email body',
                          to: 'abc@gmail.com'
            }
        }
    }
}

def isUATEnvironment() {
    return env.BRANCH_NAME == 'UAT'{
      sendTestPassEmail()
   }
}
def sendTestPassEmail() {
    emailext subject: 'UAT Test Case Passed',
              body: The test case has passed successfully.',
              to: 'abc@gmail.com'
}

Note: run-uat-tests.sh script can be written according to the build.
