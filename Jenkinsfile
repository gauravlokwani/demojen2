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

          sh 'printenv'
          sh 'sudo docker build -t glokwani2/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push glokwani2/numeric-app:""$GIT_COMMIT""'

      }
    }
    }
}
