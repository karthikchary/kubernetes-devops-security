pipeline {
  agent any
  tools {
    maven "MyMaven"
  }
  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }

      stage('Unit Test - Junit and Jacoco') {
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
          sh 'docker build -t karthikchary/numeric-app:""$GIT_COMMIT"" .'
          sh 'docker push karthikchary/numeric-app:""$GIT_COMMIT""'
        }
      }   
    }
}