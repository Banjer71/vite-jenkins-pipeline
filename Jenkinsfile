pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')   // Jenkins Credential ID
        REPO = "github.com/Banjer71/vite-jenkins-pipeline"
        BRANCH = "gh-pages"
    }

    stages {

        // Step 1: Install NodeJS tool
        stage('Declarative: Tool Install') {
            steps {
                tool name: 'node22', type: 'NodeJS'
            }
        }

        // Step 2: Check Node/NPM versions
        stage('Check Node') {
            steps {
                script {
                    def nodeHome = tool name: 'node22', type: 'NodeJS'
                    env.PATH = "${nodeHome}/bin:${env.PATH}"
                    sh 'node -v'
                    sh 'npm -v'
                }
            }
        }

        // Step 3: Configure Vite base path for GitHub Pages
        stage('Configure Vite base path') {
            steps {
                sh '''
                    echo "Setting Vite base path for GitHub Pages"
                    cp vite.config.js vite.config.js.bak
                    sed -i "s|defineConfig({|defineConfig({ base: '/vite-jenkins-pipeline/',|g" vite.config.js
                '''
            }
        }

        // Step 4: Install dependencies
        stage('Install dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        // Step 5: Build the Vite app
        stage('Build Vite app') {
            steps {
                sh 'npm run build'
            }
        }

        // Step 6: Prepare deployment folder
        stage('Prepare deployment folder') {
            steps {
                sh '''
                    rm -rf deploy
                    mkdir deploy
                    cp -R dist/* deploy/
                '''
            }
        }

        // Step 7: Deploy to GitHub Pages
        stage('Deploy to GitHub Pages') {
            steps {
                sh '''
                    cd deploy
                    git init
                    git config user.name "Jenkins CI"
                    git config user.email "jenkins@example.com"
                    git add -A
                    git commit -m "Deploy from Jenkins - ${BUILD_NUMBER}"
                    git branch -M main
                    git remote add origin https://${GITHUB_TOKEN}@${REPO}
                    git push --force origin main:${BRANCH}
                '''
            }
        }
    }
}
