pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' 
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
              withDockerRegistry([credentialsId: "docker-hub", url:""]) {
                sh 'printenv'
                sh 'docker build -t adelzli/numeric-app:""$GIT_COMMIT"" .'
                sh 'docker push adelzli/numeric-app:""$GIT_COMMIT""'
            }}
        }  
      stage('Kubernetes Deployment - DEV') {
            steps {
              withkubeConfig([credentialsId: 'kubeconfig']) {
                sh "sed -i 's#replace#adelzli/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                sh "kubectl apply -f k8s_deployment_service.yaml"
            }}
        }   
    }
}