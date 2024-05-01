pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        } 

       stage('unit testing') {
            steps {
              sh"mvn test"
            }
       }
       post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco(execPattern: 'target/jacoco.exec')
        }
       }
      stage('Docker build and push') {
            steps {
              withDocker registry([credentialsID: "docker-hub", url: ""]){
                sh('print env')
                sh('docker build -t lpsandeep09/numeric-app:""$GIT_COMMIT"" . ')
                sh('docker push lpsandeep09/numeric-app:""$GIT_COMMIT""')
        }
      }
    }
      stage('Kubernetes Deployment - Dev') {
            steps {
              withKubeConfig([credentialsID: 'kubeconfig']){
              sh "sed -i 's#replace#lpsandeep09/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              sh "kubectl -f apply k8s_deployment_service.yaml"  
              }
    }              
  }
}



