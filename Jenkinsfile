pipeline {
   agent any
   environment {
      DOCKER_HOST = 'unix:///Users/guangsongxia/.docker/run/docker.sock'
   }
   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }
      stage('Docker Build') {
         steps {
            sh(script: 'docker compose build')
         }
      }
      stage('Start App') {
         steps {
            sh(script: 'docker compose up -d')
         }
      }
      stage('Docker Push') {
         steps {
            echo "Runnning in $WORKSPACE"
            dir("$WORKSPACE/azure-vote") {
               script {
                  docker.withRegistry('', 'dockerhub') {
                     def image = docker.build('gators/jenkins-course:2024')
                     image.push()
                  }
               }
            }
         }
      }
      stage('Run Grype') {
         steps {
            grypeScan autoInstall: false, repName: 'grypeReport_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:gators/jenkins-course:2023'
         }
         post {
            always {
               recordIssues(
                  tools: [grype()],
                  aggregatingResults: true,
               )
            }
         }
      }
   }
   post {
      always {
         sh(script: 'docker compose down')
      }
   }
}