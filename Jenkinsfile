pipeline {
    agent any

    tools {
        maven 'Maven-3.8.1'   // 确保 Jenkins 全局配置中有此 Maven
        jdk 'JDK-17'          // 确保有 JDK 11
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Install (build & install core)') {
            steps {
                bat 'mvn clean install -DskipTests'   // 关键：安装所有模块到本地仓库
            }
        }

        stage('PMD Code Check') {
            steps {
                bat 'mvn pmd:pmd'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Generate Test Report') {
            steps {
                // 生成 HTML 报告（需要 maven-surefire-report-plugin）
                bat 'mvn surefire-report:report'
                publishHTML([
                    reportDir: 'target/site',
                    reportFiles: 'surefire-report.html',
                    reportName: 'Test Report'
                ])
            }
        }

        stage('Generate JavaDoc & Package') {
            steps {
                bat 'mvn javadoc:jar'
                bat 'mvn package -DskipTests'   // 生成可执行 JAR
            }
        }
    }

    post {
        always {
            // 归档产物
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            archiveArtifacts artifacts: 'target/*-javadoc.jar', allowEmptyArchive: true
            archiveArtifacts artifacts: 'target/surefire-reports/*', allowEmptyArchive: true
        }
    }
}
