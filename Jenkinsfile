pipeline {
  agent any
  tools {
    maven "MyMaven"
  }
  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar'
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

      stage('Mutation tests - PIT') {
        steps {
          sh "mvn org.pitest:pitest-maven:mutationCoverage"
        }
        // post {
        //   always {
        //     pitmutaion mutationStatsFile: '**/target/pit-reports/**/mutation.xml'
        //   }
        // }
      }
   
      stage('SonarQube Analysis') {
        steps{
          sh "mvn sonar:sonar -Dsonar.projectKey=numeric-application -Dsonar.login=sqa_29e44a6e7401840cf8178bbe670f1fc01045c878"
        }
      }

      stage('Docker Build and Push') {
        steps {
          withDockerRegistry([credentialsId: "docker-hub",url:""]) {
            sh 'printenv'
            sh 'docker build -t karthikchary/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push karthikchary/numeric-app:""$GIT_COMMIT""'
          }
        }
      }   
    }
}
