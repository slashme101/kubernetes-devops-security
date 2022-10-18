pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that, they can be downloaded later
            }
        }   

        



        stage('Unit Tests - JUnit and Jacoco') {
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
            withDockerRegistry([credentialsId: "docker-hub", url: ""]){
            sh 'printenv'
            sh 'docker build -t slashme101/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push slashme101/numeric-app:""$GIT_COMMIT""'
            }
          }
        }

        stage('Kubernetes Deployment - DEV') {
          steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {
              sh "sed -i 's#replace#slashme101/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              sh "kubectl apply -f k8s_deployment_service.yaml"
            }
          }
        }
    }
}