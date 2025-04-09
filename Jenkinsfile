pipeline {
  agent any
  tools { 
    maven 'MAVEN_3_6_3'  
  }
  
  stages {
    stage('Compile and Run Sonar Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=awsfullofbugsapp -Dsonar.organization=awsfullofbugsapp -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=$SONAR_TOKEN'
        }
      }
    }

    stage('Run SCA Analysis Using Snyk') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          sh 'mvn snyk:test -fn'
        }
      }
    }

    stage('Build') { 
      steps { 
        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("mse")
          }
        }
      }
    }

    stage('Push') {
      steps {
        script {
          docker.withRegistry('https://772440236386.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
            app.push("latest")
          }
        }
      }
    }
  }
}
