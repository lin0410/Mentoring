pipeline {
  agent any 
  tools{
        jdk 'jdk17'
        nodejs 'node20'
  }
  environment{
    DOCKER_HUB_CREDENTIALS = credentials('docker-credentials')
    EMAIL_ADDRESS = "kankoffi36@gmail.com"
    REPOSITORY_DOCKER_HUB =" ikhela/mentoring"
    // SCANNER_HOME=tool 'sonar-scanner'
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
    stage("Check-git-secrets"){
      steps{
          sh 'rm trufflehog || true'
          sh 'docker run gesellix/trufflehog --json https://github.com/ikhela-04KK/Mentoring.git > trufflehog'
          sh 'cat trufflehog'
      }
    }
    // stage("Sonarqube Analysis "){
    //     steps{
    //         withSonarQubeEnv('sonar-server') {
    //               sh '''sonar-scanner \
    //                     -Dsonar.projectKey=Mentoring \
    //                     -Dsonar.sources=. \
    //                     -Dsonar.host.url=https://9000-lin0410-mentoring-p6mvl05p8k7.ws-eu108.gitpod.io \
    //                     -Dsonar.token=sqp_0e8da3d1939cc8d8e37c390ac0cf1610f9cb903b
    //                 '''
    //         }
    //     }
    // }
    // stage("quality gate"){
    //   steps {
    //         script {
    //             waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token' 
    //         }
    //     } 
    // }
    // install dependencies 
    // for owasp scan 
    stage("OWASP FS SCAN"){
      steps{
        dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'OWASP-DP-CHECK'
        dependencyCheckPublish pattern: '**/dependency-check-report.xml'
      }
    }
    // add trivy Scan for image 
    stage('TRIVY FS SCAN'){
      steps{
        sh 'trivy fs . > trivyfs.txt'
      }
    }

    stage("build") {  
      steps {
        sh 'docker compose -f docker-compose.yml build'
      }
    }
    stage('login Docker hub') {
      steps {
        sh 'docker login -u $DOCKER_HUB_CREDENTIALS_USR -p $DOCKER_HUB_CREDENTIALS_PSW'
      }
    }
    stage('tag docker image '){
      steps{
        sh 'docker tag web-next:latest ikhela/mentoring:web-next'
        sh 'docker tag api-nest:latest ikhela/mentoring:api-nest'
      }
    }
    // scan trivy image 
    stage('Trivy scan image'){
      steps{
        sh 'trivy --no-progress --exit-code 1 severity HIGH,CRITICAL ikhela/mentoring:web-next'
        sh 'trivy --no-progress --exit-code 1 severity HIGH,CRITICAL ikhela/mentoring:api-nest'
      }
    }
    stage('push docker image '){
      steps{
        sh 'docker push ikhela/mentoring:web-next'
        sh 'docker push ikhela/mentoring:api-nest'
      }
    }
    stage('Clean') {
      steps {
        // Supprimer les images locales non utilisées
        sh 'docker image rm api-nest:latest'
        sh 'docker image rm web-next:latest'
        sh "docker logout"
      }
    }
    stage("Deploy to container") {  
     steps {
       sh 'docker compose -f docker-compose.yml up -d'
     }
   }
  //  stage("DAST") {
  //    steps {
  //        sshagent(credentials: ['zap']) {
  // sh "ssh -o StrictHostKeyChecking=no ubuntu@ec2-13-211-212-239.ap-southeast-2.compute.amazonaws.com 'docker run -t owasp/zap2docker-stable zap-baseline.py -t http://13.211.212.234:8080/webapp' || true "
  //        }
  //    }
  //  }
    // stage('Email Notification'){
    //   steps {
    //     script{
    //       // Send email notification 
    //       mail to: 'kankoffi36@gmail.com', 
    //             subject: "Jenkins Build Notification",
    //             body: "Pipeline: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n\n${BUILD_URL}\n\n Succes build and push image"
    //     }
    //   }
    // }
  }
  post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'lingardkent@gmail.com',  
            attachmentsPattern: 'trivyfs.txt,trivyimage.txt'
        }
    }
}
