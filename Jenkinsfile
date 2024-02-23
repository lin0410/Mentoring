pipeline {
  agent any 

  environment{
      DOKCER_HUB_CREDENTIALS = credentials('crd_docker_hub')
      EMAIL_ADDRESS = "kankoffi36@gmail.com"
      REPOSITORY_DOCKER_HUB =" ikhela/mentoring"
  }

  stages {
    stage("verify tooling") {
      steps {
        sh '''
            docker -v
            docker compose -v
           '''
      }
    }
    
    stage("build") {  
      steps {
        // sh 'docker compose -f docker-compose.yml build'
        sh "echo hello world"
      }
    }

    stage('login Docker hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: DOCKER_HUB_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
            sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
        }
      }
    }

    stage('tag docker image '){
      steps{
        // sh 'docker tag web-next:latest ikhela/mentoring:web-next'
        // sh 'docker tag api-nest:latest ikhela/mentoring:api-nest'
        sh "echo hello world"
      }
    }

    stage('push docker image '){
      steps{
        // sh 'docker push ikhela/mentoring:web-next'
        // sh 'docker push ikhela/mentoring:api-nest'
        sh "echo hello word"
      }
    }

    stage('Clean') {
      steps {
        // Supprimer les images locales non utilisées
        // sh 'docker image rm api-nest:latest'
        // sh 'docker image rm web-next:latest'
        // sh "docker logout"
        sh "echo hello word"
      }
    }
  
    stage('Email Notification'){
      steps {
        script{
          // Send email notification 
          mail  to: 'kankoffi36@gmail.com', 
                subject: "Jenkins Build Notification",
                body: "Pipeline: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n\n${BUILD_URL}\n\n Succes build and push image"
        }
      }
    }
  }
}
