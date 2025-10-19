pipeline {
    agent any

    triggers {
        // Tự động chạy khi có push từ GitHub
        githubPush()
    }

    environment {
        GIT_USER = "Jenkins Bot"
        GIT_EMAIL = "jenkins@yourdomain.com"
        // ⚠️ Thay bằng Personal Access Token của bạn
        GITHUB_TOKEN = credentials('test-github-jenkins-key')  
        // credentials này bạn tạo trong Jenkins Credentials
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone repo mà webhook gửi tới
                checkout scm
            }
        }

        stage('Modify File') {
            steps {
                sh '''
                echo "Auto update by Jenkins at $(date)" >> auto_log.txt
                cat auto_log.txt
                '''
            }
        }

        stage('Commit & Push Changes') {
            steps {
                sh '''
                git config --global user.name "${GIT_USER}"
                git config --global user.email "${GIT_EMAIL}"

                # Đặt lại remote với token để có quyền push
                git remote set-url origin https://${GITHUB_ACTOR}:${GITHUB_TOKEN}@github.com/${GIT_URL#*github.com/}

                git add auto_log.txt
                git commit -m "Auto update by Jenkins at $(date)" || echo "No changes to commit"
                git push origin HEAD:${GIT_BRANCH}
                '''
            }
        }

        stage('Build Confirmation') {
            steps {
                echo "Build completed and changes pushed successfully."
            }
        }
    }

    post {
        failure {
            echo "❌ Build failed. Check logs above."
        }
        success {
            echo "🎉 Pipeline ran successfully!"
        }
    }
}
