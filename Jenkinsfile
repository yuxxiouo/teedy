pipeline {
    agent any

    tools {
        maven 'Maven-3.8.1'
        jdk 'JDK-17'
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Maven Install') {
            steps { bat 'mvn clean install -DskipTests' }
        }

        stage('PMD Code Check') {
            steps { bat 'mvn pmd:pmd' }
        }

        stage('Run Tests') {
            steps { bat 'mvn test -Dmaven.test.failure.ignore=true' }
        }

        stage('Generate Test Report') {
            steps {
                bat 'mvn surefire-report:report-only'
                publishHTML(target: [
    allowMissing: false,
    alwaysLinkToLastBuild: false,
    keepAll: true,
    reportDir: 'target/site/',
    reportFiles: 'surefire-report.html',
    reportName: 'Maven Surefire Reportt'
])
            }
        }

        stage('Generate JavaDoc & Package') {
            steps {
                bat 'mvn javadoc:jar -Dmaven.javadoc.failOnError=false'
                bat 'mvn package -DskipTests'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            archiveArtifacts artifacts: 'target/*-javadoc.jar', allowEmptyArchive: true
            archiveArtifacts artifacts: 'target/surefire-reports/*', allowEmptyArchive: true
        }
    }
}
