pipeline {
    environment {
        // Project
        projectName = 'WebGoat QA Lovers Day'
        // SonarQube
        projectKey="QALoversDay.WebGoat"
    }

    agent any

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
                withSonarQubeEnv('SonarQube-Qalitax') {
                    withMaven(maven: 'Maven 3') {
                        bat "mvn $SONAR_MAVEN_GOAL -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.branch.name=$BRANCH_NAME -Dsonar.projectName=$projectName"
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