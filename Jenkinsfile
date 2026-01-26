pipeline {
  agent any

  tools {
    maven "M3"
    jdk "JDK17"
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerCredential')
  }
  
  stages {
    // Git Clone
    stage('Git Clone') {
      steps {
        git url: 'https://github.com/soliangpt/spring-petclinic.git/', branch: 'main'
      }
    }
    // Maven을 이용해 Build한다.
    stage('Maven Build') {
      steps {
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'  
      }
      post {
        success {
          echo 'Maven Build Success'
        }
        failure {
          echo 'Maven Build Failure'
        } 
       }
      }
      // Docker Image 생성
      stage('Docker Image Build') {
        steps {
          echo 'Docker Image Build'
          dir("${env.WORKSPACE}") {
            sh """
            docker build -t spring-petclinic:$BUILD_NUMBER .
            docker tag spring-petclinic:$BUILD_NUMBER soliangpt/spring-petclinic:latest
            """
          }
        }
      }
      // Docker Image Upload
      stage('Docker Image Upload') {
        steps {
          echo 'Docker Image Upload'
          sh """
            echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
            docker push soliangpt/spring-petclinic:lastest
            """

        }
      }

     
    
  }
}
