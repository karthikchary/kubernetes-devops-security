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
          sh "mvn clean verify sonar:sonar  -Dsonar.projectKey=numeric-application -Dsonar.host.url=https://9000-port-aa916a991fe646ff.labs.kodekloud.com -Dsonar.login=sqp_0807529f689d4af0bd620d4f89b5ecd7719f15ed"
        }
      }

      stage('Vulnerability Scan - Dokcer') {
        steps{
          sh "mvn dependency-check:check"
        }
        post {
          always {
            dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
          }
        }
      }

      stage('Docker Build and Push') {
        steps {
          // withDockerRegistry([credentialsId: "docker-hub",url:""]) {
            sh 'printenv'
            sh 'docker build -t karthikchary/numeric-app:""$GIT_COMMIT"" .'
            // sh 'docker push karthikchary/numeric-app:""$GIT_COMMIT""'
          // }
        }
      }   
    }
}
