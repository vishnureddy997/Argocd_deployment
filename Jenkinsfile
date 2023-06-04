pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        sh 'echo passed'
        git branch: 'main', url: 'https://github.com/vishnureddy997/Argocd_deployment.git'
      }
    }
     stage('Set Variables') {
      steps {
        script {
          env.APP_VERSION = '1.0.0' // Set the desired version number
          env.IMAGE_VERSION = 'v1.2.3' // Set the desired image version
        }
      }
    }
    stage('Build and Push Docker Image') {
      environment {
        DOCKER_IMAGE = "dockerrepository123/testnodeapp:${APP_VERSION}"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
      }
      steps {
        script {
            sh 'docker build -t ${DOCKER_IMAGE} .'
            def dockerImage = docker.image("${DOCKER_IMAGE}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                dockerImage.push()
            }
        }
      }
    }
    stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "Argocd_deployment"
            GIT_USER_NAME = "vishnureddy997"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "vishnureddy14ma@gmail.com"
                    git config user.name "Vishnu Reddy"
                   // BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/replaceImageTag/${APP_VERSION}/g" deployment.yml
                    git add deployment.yml
                    git commit -m "Update deployment image to version ${APP_VERSION}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
            }
        }
    }
  }
}
