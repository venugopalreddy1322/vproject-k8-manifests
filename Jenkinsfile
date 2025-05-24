pipeline {
    agent any
    environment {
        GIT_REPO = 'https://github.com/venugopalreddy1322/vproject-k8-manifests.git'
        //GIT_CREDENTIALS_ID = 'github'
    }

    stages {
        stage('Checkout Kubernetes Manifest Repo') {
            steps {
                checkout scm
            }
        }

        stage('Update Image in Manifest') {
            steps {
                script {
                    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                        sh "cat deployment.yaml"
                        sh "sed -i 's|venugopalreddy1322/vproject-k8-manifests.*|venugopalreddy1322/vproject-k8-manifests:${DOCKERTAG}|g' deployment.yaml"
                        sh "cat deployment.yaml"
                        withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                            sh "git config --global user.email 'venugopalreddy13222@gmail.com.com'"
                            sh "git config --global user.name 'Venugopal'"
                            sh "git remote set-url origin https://${GIT_USER}:${GIT_PASS}@github.com/venugopalreddy1322/vproject-k8-manifests.git"
                            sh "git add deployment.yaml"
                            sh "git commit -m 'Updated image to ${DOCKERTAG}'"
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
