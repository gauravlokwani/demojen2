pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be Test 1
            }
        }
      stage('UNIT test & jacoco ') {
            steps {
              sh "mvn test"
            }
            post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
     
        }
     stage('Docker Build and Push') {
      steps {
          withCredentials([string(credentialsId: 'dockerpassglokwani', variable: 'DOCKER_HUB_PASSWORD')]) {
              sh 'sudo docker login -u hrefnhaila -p $DOCKER_HUB_PASSWORD'
              sh 'printenv'
              sh 'sudo docker build -t hrefnhaila/devops-app:""$GIT_COMMIT"" .'
              sh 'sudo docker push hrefnhaila/devops-app:""$GIT_COMMIT""'
          }
        
      }
    }
    }
}
