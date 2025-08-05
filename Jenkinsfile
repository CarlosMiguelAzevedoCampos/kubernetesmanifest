node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh '''
                        git config user.email "carlosmiguelcampos1996@gmail.com"
                        git config user.name "Carlos"

                        echo "Before change:"
                        cat deployment.yaml

                        sed -i 's+carloscamposdev/test.*+carloscamposdev/test:${DOCKERTAG}+g' deployment.yaml

                        echo "After change:"
                        cat deployment.yaml

                        git add .
                        git commit -m "Done by Jenkins Job changemanifest: ${BUILD_NUMBER}"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetesmanifest.git HEAD:main
                    '''
                }
            }
        }
    }
}
