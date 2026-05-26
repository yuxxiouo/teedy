pipeline {
    agent any

    tools {
        maven 'Maven-3.8.1'   // 需在 Jenkins 全局工具配置中定义 Maven 名称
        jdk 'JDK-17'          // 需配置 JDK 工具名称
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
    steps {
        bat 'mvn clean compile'
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
                // 生成 HTML 格式测试报告（需插件：HTML Publisher）
                publishHTML([
                    reportDir: 'target/surefire-reports',
                    reportFiles: '*.html',
                    reportName: 'Test Report',
                    allowMissing: true
                ])
            }
        }

        stage('Generate JavaDoc & Package') {
            steps {
                bat 'mvn javadoc:jar'   // 生成包含 JavaDoc 的 JAR 包
                bat 'mvn package'       // 生成可执行 JAR（如 spring-boot:repackage）
            }
        }
    }

    post {
        always {
            // 归档构建产物
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            // 归档 JavaDoc JAR（如果单独生成）
            archiveArtifacts artifacts: 'target/*-javadoc.jar', allowEmptyArchive: true
            // 也可归档测试报告目录
            archiveArtifacts artifacts: 'target/surefire-reports/*', allowEmptyArchive: true
        }
    }
}
