pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        git branch: 'main', url: 'https://github.com/vishnureddy997/Argocd_deployment.git'
      }
    }
    stage('Build and Push Docker Image') {
      steps {
        script {
          def imageTag = "v1.0.${env.BUILD_NUMBER}" // Generate image tag using Jenkins build number
          def dockerImage = docker.build("dockerrepository123/testnodeapp:${imageTag}", ".")
          docker.withRegistry('https://index.docker.io/v1/', 'docker-cred') {
            dockerImage.push()
          }
          env.IMAGE_TAG = imageTag // Store the image tag in an environment variable for future 
        }
      }
    }
    stage('Deleting Docker Images') {
      steps {
        sh 'docker rmi $(docker images -q)'
      }
    }
    stage('Update Deployment File') {
      steps {
        script {
          sh """
            sed -i 's|image: dockerrepository123/testnodeapp:.*|image: dockerrepository123/testnodeapp:${env.IMAGE_TAG}|' deploymentfiles/deployment.yml
            cat deploymentfiles/deployment.yml
          """
        }
      }
    }
    stage('Commit and Push Deployment File') {
        environment {
            GIT_REPO_NAME = "Argocd_deployment"
            GIT_USER_NAME = "vishnureddy997"
        }
      steps {
        script {
          withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
            sh """
              git config user.email "vishnureddy14ma@gmail.com"
              git config user.name "Vishnu Reddy"
              git add deploymentfiles/deployment.yml 
              git commit -m "Update deployment image to version ${env.BUILD_NUMBER}" deployment.yml
              git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
            """
          }
        }
      }
    }
  }
}
