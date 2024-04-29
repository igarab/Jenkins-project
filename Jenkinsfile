pipeline {
    agent any

    stages {
        stage("Checkout") {
            steps {
                // list the workspace content before pulling code from git - NO files
                sh "ls" 
                git branch:'main', url: 'https://github.com/igarab/Jenkins-project'
                // list the workspace content after pulling the code from git - all git files 
                sh "ls"
            }
        }
        
        stage("Build & Test") {
            steps {
                sh "./gradlew clean build"
                // sh "./gradlew assemble"
            }
        }
        
        stage('Parallel Tests') {
            steps {    
                parallel(
                    testsA: {
                        sh "echo test set A"
                        sleep 2
                        sleep 3
                    }, testsB: {
                        sh "echo test set B"
                        sleep 4
                    }, testsC: {
                        sleep 1
                        // sh "false"
                    }
                )
            }
            failFast true
        } 
        
        stage("Archive Results") {
            steps {
                archiveArtifacts artifacts: '**/libs/*.jar'
            }
        }
        
        stage("Publish Test Results") {
            steps {
                jacoco()
                junit stdioRetention: '', testResults: '**/test-results/test/*.xml'
            }
        }
    }
    
    post {
        always {
            emailext body: "${env.BUILD_URL}\n${currentBuild.absoluteUrl}", 
            to: 'always@foo.bar',
            recipientProviders: [previous()], 
            subject: "${currentBuild.currentResult}: Job ${env.JOB_NAME} [${env.BUILD_NUMBER}]"
        }
    }
}