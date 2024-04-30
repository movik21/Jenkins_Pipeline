pipeline {
    agent any

    tools {
        nodejs 'NodeJS 20.10.0' // Specify needed nodejs installation where npm installed packages will be provided to the PATH
    }

    stages {
        stage('Build Environment') {
            steps{
                echo 'Environment..'
                
                // Delete Workspace before Build starts
                deleteDir()    

                // Get some code from a GitHub repository
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/movik21/DevOpsDemo.git']])

                // Check tools version
                echo "Node.js version: ${sh(returnStdout: true, script: 'node --version').trim()}"
                echo "npm version: ${sh(returnStdout: true, script: 'npm --version').trim()}"
            }
        }

        stage('Build') {
            steps {
                echo 'Building..'

                // Build Step: Invoke Gradle script
                dir('backend') {
                    sh 'chmod +x ./gradlew'
                    sh './gradlew test'
                }

                // Build Step: Install and run NPM for frontend
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                    }
            }
        }

    }

    post {
        always {
            junit '**/test-results/test/*.xml'
            jacoco(
                execPattern: '**/**.exec', // Path to exec files (e.g.: **/target/**.exec, **/jacoco.exec)
                classPattern: '**/classes', // Path to class directories (e.g.: **/target/classDir, **/classes)
                sourcePattern: '**/src/main/java', // Path to source directories (e.g.: **/mySourceFiles)
            )
        }
        success {
            echo 'Build and tests succeeded.'
        }
        failure {
            echo 'Build or tests failed.'
        }
    }
}