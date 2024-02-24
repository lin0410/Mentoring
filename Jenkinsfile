pipeline {
  agent any 
  tools{
        jdk 'jdk17'
        nodejs 'node16'
  }
  environment{
    DOKCER_HUB_CREDENTIALS = credentials('mentoring-dockerhub')
    EMAIL_ADDRESS = "kankoffi36@gmail.com"
    REPOSITORY_DOCKER_HUB =" ikhela/mentoring"
    SCANNER_HOME=tool 'sonar-scanner'
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
    stage("Sonarqube Analysis "){
        steps{
            withSonarQubeEnv('sonar-server') {
                   sh '''${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=Mentoring \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.token=sqp_f5bdd10488ec6f31d383222da6dbea2114693201
                    '''
            }
        }
    }
    stage("quality gate"){
       steps {
            script {
                waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
            }
        } 
    }
    // stage("build") {  
    //   steps {
    //     sh 'docker compose -f docker-compose.yml build'
    //   }
    // }

    stage('login Docker hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: DOCKER_HUB_CREDENTIALS, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
            sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
        }
      }
    }

    // stage('tag docker image '){
    //   steps{
    //     sh 'docker tag web-next:latest ikhela/mentoring:web-next'
    //     sh 'docker tag api-nest:latest ikhela/mentoring:api-nest'
    //   }
    // }

    // stage('push docker image '){
    //   steps{
    //     sh 'docker push ikhela/mentoring:web-next'
    //     sh 'docker push ikhela/mentoring:api-nest'
    //   }
    // }

    stage('Clean') {
      steps {
        // Supprimer les images locales non utilisées
        // sh 'docker image rm api-nest:latest'
        // sh 'docker image rm web-next:latest'
        sh "docker logout"
      }
    }
  
    stage('Email Notification'){
      steps {
        script{
          // Send email notification 
          mail to: 'kankoffi36@gmail.com', 
                subject: "Jenkins Build Notification",
                body: "Pipeline: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n\n${BUILD_URL}\n\n Succes build and push image"
        }
      }
    }
  }
}
