pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'New Docker image tag')
    }

    environment {
        GIT_REPO = 'https://github.com/venugopalreddy1322/k8-manifest.git'
        GIT_CREDENTIALS_ID = 'git-credentials'
    }

    stages {
        stage('Checkout Kubernetes Manifest Repo') {
            steps {
                git branch: 'main', credentialsId: env.GIT_CREDENTIALS_ID, url: env.GIT_REPO
            }
        }

        stage('Update Image in Manifest') {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        sh "sed -i 's|image: .*|image: ${params.IMAGE_TAG}|' deployment.yaml"
                        withCredentials([usernamePassword(credentialsId: env.GIT_CREDENTIALS_ID, usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                            sh "git config --global user.email 'jenkins@company.com'"
                            sh "git config --global user.name 'Jenkins'"
                            sh "git remote set-url origin https://${GIT_USER}:${GIT_PASS}@github.com/venugopalreddy1322/k8-manifest.git"
                            sh "git add deployment.yaml"
                            sh "git commit -m 'Updated image to ${params.IMAGE_TAG}'"
                            sh "git push origin main"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Kubernetes Manifest Updated Successfully! ArgoCD will deploy the changes.'
        }
        unstable {
            echo 'Pipeline completed with issues. Check logs!'
        }
        failure {
            echo 'Pipeline Failed. Check logs!'
        }
    }
}
