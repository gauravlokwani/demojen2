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
    stage('Mutation Tests - PIT') {

      steps {

        sh "mvn org.pitest:pitest-maven:mutationCoverage"

      }

        post { 

         always { 

           pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'

         }

       }

    }
    stage('SonarQube - SAST') {

           steps {

 
withCredentials([string(credentialsId: 'token-sonar-glokwani', variable: 'TOKEN-SONAR')]) {
            sh "mvn clean verify sonar:sonar -Dsonar.projectKey=myproject -Dsonar.projectName='myproject'-Dsonar.host.url=http://demo-test2.eastus.cloudapp.azure.com:9010 -Dsonar.token=$TOKEN-SONAR"
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
