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
      stage('Run Tests') {
         steps {
            script {
               def containerId = sh(script: "docker ps -q -f name=<container_name>", returnStdout: true).trim()
               sh(script: "docker exec ${containerId} pytest /app/tests/test_sample.py")
            }
         }
         post {
            success {
               echo "Tests passed! :)"
            }
            failure {
               echo "Tests failed :("
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