pipeline {
    agent any

    tools {
        // 确保 Jenkins 全局工具配置中有名为 'Maven-3.8.1' 和 'JDK-11' 的工具
        maven 'Maven-3.8.1'
        jdk 'JDK-17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // 关键：先 install 所有模块，解决 docs-core 依赖问题
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

        // 运行测试，允许失败，以便继续生成报告
        stage('Run Tests') {
            steps {
                bat 'mvn test -Dmaven.test.failure.ignore=true'
            }
        }

        // 生成 HTML 测试报告（需 HTML Publisher 插件）
        stage('Generate Test Report') {
            steps {
                // 生成 surefire-report.html
                bat 'mvn surefire-report:report-only -Dmaven.test.failure.ignore=true'
                // 发布报告，注意插件安装：HTML Publisher
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

        // 生成 JavaDoc JAR 并打包可执行 JAR
        stage('Generate JavaDoc & Package') {
            steps {
                // 忽略 JavaDoc 语法错误
                bat 'mvn javadoc:jar -Dmaven.javadoc.failOnError=false'
                // 打包最终 JAR，跳过测试（测试已经运行过）
                bat 'mvn package -DskipTests -Dmaven.javadoc.skip=true'
            }
        }
    }

    post {
        always {
            // 归档所有产物
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            archiveArtifacts artifacts: 'target/*-javadoc.jar', allowEmptyArchive: true
            // 归档 XML 格式的测试报告（JUnit 插件也可用）
            archiveArtifacts artifacts: '**/target/surefire-reports/*.xml', allowEmptyArchive: true
        }
    }
}
