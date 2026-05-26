// Jenkinsfile for Teedy CI Pipeline
pipeline {
    agent any  // 使用任意可用节点
    tools {
        maven 'Maven-3.8.1'  // Jenkins中配置的Maven工具名称
        jdk 'JDK-17'      // Jenkins中配置的JDK名称
    }
    stages {
        // 1. 拉取代码（SCM已配置，自动执行）
        stage('拉取代码') {
            steps {
                git url: 'https://github.com/yuxxiouo/Teedy.git', branch: 'main'
            }
        }

        // 2. Maven构建项目
        stage('Maven构建') {
            steps {
                sh 'mvn clean package -DskipTests'  // 打包，跳过测试
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true  // 归档jar包
                }
            }
        }

        // 3. PMD静态代码检查
        stage('PMD代码检查') {
            steps {
                sh 'mvn pmd:pmd'  // 执行PMD代码检查
            }
            post {
                always {
                    pmd canComputeNew: false, defaultEncoding: '', healthy: '', pattern: 'target/pmd.xml', unHealthy: ''
                }
            }
        }

        // 4. 运行单元测试
        stage('运行测试') {
            steps {
                sh 'mvn test'  // 执行测试用例
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'  // 展示测试结果
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'target/site',
                        reportFiles: 'index.html',
                        reportName: '测试报告'
                    ])
                }
            }
        }

        // 5. 生成JavaDoc文档
        stage('生成JavaDoc') {
            steps {
                sh 'mvn javadoc:jar'  // 生成JavaDoc jar包
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*-javadoc.jar', fingerprint: true  // 归档文档包
                }
            }
        }
    }
    // 构建后操作
    post {
        always {
            echo '流水线执行完成！'
        }
    }
}
