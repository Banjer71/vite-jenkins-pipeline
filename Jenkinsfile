pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')
        REPO = "github.com/Banjer71/vite-jenkins-pipeline"
        BRANCH = "gh-pages"
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Verify NodeJS Tool') {
            steps {
                script {
                    try {
                        // Use the NodeJS tool configured in Jenkins → Global Tool Configuration
                        def nodeHome = tool name: 'node22', type: 'NodeJS'
                        env.PATH = "${nodeHome}/bin:${env.PATH}"
                        def nodeVersion = sh(script: 'node -v', returnStdout: true).trim()
                        echo "NodeJS found at ${nodeHome}, version: ${nodeVersion}"

                        // Check Node version >= 22.12
                        def versionNumbers = nodeVersion.replace('v','').tokenize('.').collect{ it.toInteger() }
                        if (versionNumbers[0] < 22 || (versionNumbers[0] == 22 && versionNumbers[1] < 12)) {
                            error "Node.js version must be 22.12+ for Vite. Found: ${nodeVersion}"
                        }

                        // Print npm version
                        sh 'npm -v'
                    } catch (Exception e) {
                        error "NodeJS tool 'node22' not found! Please configure it in Jenkins → Global Tool Configuration."
                    }
                }
            }
        }

        stage('Configure Vite base path') {
            steps {
                sh '''
                    echo "Setting Vite base path for GitHub Pages"
                    cp vite.config.js vite.config.js.bak
                    sed -i "s|defineConfig({|defineConfig({ base: '/vite-jenkins-pipeline/',|g" vite.config.js
                '''
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Build Vite app') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Prepare deployment folder') {
            steps {
                sh '''
                    rm -rf deploy
                    mkdir deploy
                    cp -R dist/assets dist/index.html dist/vite.svg deploy/
                '''
            }
        }

        stage('Deploy to GitHub Pages') {
            steps {
                dir('deploy') {
                    sh '''
                        git init
                        git config user.name "Jenkins CI"
                        git config user.email "jenkins@example.com"
                        git add -A
                        git commit -m "Deploy from Jenkins - ${BUILD_NUMBER}"
                        git branch -M main
                        git remote add origin https://${GITHUB_TOKEN}@github.com/Banjer71/vite-jenkins-pipeline.git
                        git push --force origin main:gh-pages
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Deployment complete! Check your GitHub Pages at https://Banjer71.github.io/vite-jenkins-pipeline/"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
