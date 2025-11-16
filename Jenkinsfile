pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('github-token')   // Jenkins Credential ID
        REPO = "github.com/Banjer71/vite-jenkins-pipeline"
        BRANCH = "gh-pages"
    }

    stages {
        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Vite app') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to GitHub Pages') {
            steps {
                sh '''
                    rm -rf deploy
                    mkdir deploy
                    cp -R dist/* deploy/

                    cd deploy
                    git init
                    git config user.name "Jenkins CI"
                    git config user.email "jenkins@example.com"
                    git add .
                    git commit -m "Deploy from Jenkins"

                    git branch -M main
                    git remote add origin https://${GITHUB_TOKEN}@${REPO}
                    git push --force origin main:${BRANCH}
                '''
            }
        }
    }
}
