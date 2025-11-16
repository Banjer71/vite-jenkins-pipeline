pipeline {
    agent any

    tools {
        nodejs "node22"
    }

    environment {
        GITHUB_TOKEN = credentials('github-token')
        REPO = "github.com/Banjer71/vite-jenkins-pipeline"
        BRANCH = "gh-pages"
    }

    stages {
        stage('Check Node') {
            steps {
                sh 'node -v'
                sh 'npm -v'
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
                sh '''
                    cd deploy
                    git init
                    git config user.name "Jenkins CI"
                    git config user.email "jenkins@example.com"
                    git add -A
                    git commit -m "Deploy from Jenkins - ${BUILD_NUMBER}" || true
                    git branch -M main
                    git remote add origin https://${GITHUB_TOKEN}@${REPO}
                    git push --force origin main:${BRANCH}
                '''
            }
        }
    }
}
