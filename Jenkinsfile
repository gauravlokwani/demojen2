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
              sh 'sudo docker login -u glokwani2 -p $DOCKER_HUB_PASSWORD'
              sh 'printenv'
              sh 'sudo docker build -t glokwani2/devops-app-glokwani:""$GIT_COMMIT"" .'
              sh 'sudo docker push glokwani2/devops-app-glokwani:""$GIT_COMMIT""'
          }
        
      }
    }

    stage('Deployment Kubernetes  ') {
      steps {
        withKubeConfig([credentialsId: 'kubeconfig']) {
               sh "sed -i 's#replace#glokwani2/devops-app-glokwani:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
               sh "kubectl apply -f k8s_deployment_service.yaml"
             }
      }
 
    }
    }
}
