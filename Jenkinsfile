pipeline {
    agent any
     parameters {
        string(name: 'DOCKERTAG', description: 'Docker tag to update in manifest')
    }
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
                      //sh "sed -i 's|venu1322/vproject.*|venu1322/vproject:${DOCKERTAG}|g' deployment.yaml"
                        sh "sed -i 's|image: venu1322/vproject:[^ ]*|image: venu1322/vproject:${params.DOCKERTAG}|g' deployment.yaml"
                        sh "cat deployment.yaml"
                        withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
                            sh "git config --global user.email 'venugopalreddy13222@gmail.com.com'"
                            sh "git config --global user.name 'Venugopal'"
                            sh 'git remote set-url origin https://$GIT_USER:$GIT_PASS@github.com/venugopalreddy1322/vproject-k8-manifests.git'
                            // Notice for above line single quotes '...' — they avoid interpolation and are safer for secrets.
                            sh "git add deployment.yaml"
                            sh "git commit -m 'Updated image to ${params.DOCKERTAG} by Jenkins Job '"
                            sh "git push origin HEAD:main"
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
