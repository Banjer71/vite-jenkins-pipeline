pipeline {
    agent any

    tools {
        nodejs 'node22'  // must match your Jenkins NodeJS configuration
    }

    environment {
        GITHUB_TOKEN = credentials('github-token') // Replace with your Jenkins GitHub token ID
        REPO = "github.com/Banjer71/vite-jenkins-pipeline"
        BRANCH = 'gh-pages'
        BASE_PATH = '/vite-jenkins-pipeline/'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Configure Vite base path') {
            steps {
                sh '''
                    echo "Setting Vite base path for GitHub Pages"
                    cp vite.config.js vite.config.js.bak
                    sed -i "s|defineConfig({|defineConfig({ base: '${BASE_PATH}',|g" vite.config.js
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
                    cp -R dist/* deploy/
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
                        git remote add origin https://${GITHUB_TOKEN}@github.com/${REPO}.git
                        git push --force origin main:${BRANCH}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed! Check the logs.'
        }
    }
}
