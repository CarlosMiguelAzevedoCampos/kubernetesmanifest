pipeline {
  agent any

  parameters {
    string(
      name: 'DOCKERTAG',
      defaultValue: 'latest',
      description: 'Docker image tag to deploy (e.g. latest, v1.2.3)'
    )
  }

  stages {
    stage('Clone repository') {
      steps {
        checkout scm
      }
    }

    stage('Update manifest with tag') {
      steps {
        script {
          catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
            withCredentials([
              usernamePassword(
                credentialsId: 'github',
                usernameVariable: 'GIT_USERNAME',
                passwordVariable: 'GIT_PASSWORD'
              )
            ]) {
              // Log out what Jenkins thinks the tag is:
              echo "Deploying image tag: ${params.DOCKERTAG}"

              sh '''
                git config user.email "carlosmiguelcampos1996@gmail.com"
                git config user.name  "Carlos"

                echo "=== Before ==="
                grep -R "carloscamposdev/test" deployment.yaml || true

                # Use shell variable expansion for DOCKERTAG:
                sed -i -E "s|(carloscamposdev/test:)[^[:space:]\\"]*|\\1$DOCKERTAG|g" deployment.yaml

                echo "=== After ==="
                grep -R "carloscamposdev/test" deployment.yaml || true

                git add deployment.yaml
                git commit -m "chore: update image tag to $DOCKERTAG (build #${BUILD_NUMBER})"
                git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/$GIT_USERNAME/kubernetesmanifest.git HEAD:main
              '''
            }
          }
        }
      }
    }
  }
}
