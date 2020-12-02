pipeline {

    agent any

    options {
        office365ConnectorWebhooks([[name: 'excentia-teams-notifications', notifyAborted: true, notifyBackToNormal: true, notifyFailure: true, notifyNotBuilt: true, notifyRepeatedFailure: true, notifySuccess: true, notifyUnstable: true, startNotification: true, url: 'https://outlook.office.com/webhook/f67e0fb5-cf89-4bec-acd7-4c61f5fc1e75@c81aba89-7164-45fe-b879-8ac8cad7b167/JenkinsCI/8cf6fefba0524c85b644cfab068bf966/5fea9821-8bfb-460b-a964-06700986fb84']])
    }

    stages {
        stage('Build') {
            steps {
                withMaven(maven: 'Maven 3') {
                    bat "mvn clean install -Dmaven.test.failure.ignore=true"
                }
            }
        }
        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-local') {
                    withMaven(maven: 'Maven 3') {
                        bat "mvn $SONAR_MAVEN_GOAL -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.branch.name=$BRANCH_NAME"
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Deploy') {
            steps {
                bat "mvn -pl webgoat-server spring-boot:run"
            }
        }
    }
}
