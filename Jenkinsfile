pipeline {
    agent any

    tools {
        maven 'Maven-3.8.1'
        jdk 'JDK-17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Install') {
            steps {
                bat 'mvn clean install -DskipTests'
            }
        }

        stage('PMD Code Check') {
            steps {
                bat 'mvn pmd:pmd'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'mvn test -Dmaven.test.failure.ignore=true'
            }
        }

        stage('Generate Test Report') {
            steps {
                bat 'mvn surefire-report:report-only -Dmaven.test.failure.ignore=true'
                publishHTML(target: [
                    allowMissing: true,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: 'target/site',
                    reportFiles: 'surefire-report.html',
                    reportName: 'Surefire Test Report'
                ])
            }
        }

        stage('Generate JavaDoc & Package') {
            steps {
                bat 'mvn javadoc:jar -Dmaven.javadoc.failOnError=false'
                bat 'mvn package -DskipTests -Dmaven.javadoc.skip=true'
            }
        }
    }

    post {
        always {
            // 归档所有子模块生成的 jar 文件（多模块项目）
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            // 归档 JavaDoc jar（如果生成）
            archiveArtifacts artifacts: '**/target/*-javadoc.jar', allowEmptyArchive: true
            // 归档 XML 测试报告
            archiveArtifacts artifacts: '**/target/surefire-reports/*.xml', allowEmptyArchive: true
        }
    }
}
