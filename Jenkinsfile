pipeline {
  agent any

  tools {
    maven "M3"
    jdk "JDK21"
  }
  environment {
    REGION = "ap-northeast-2"
    DOCKERHUB_CREDENTIALS = credentials('DockerCredential')
    AWS_CREDENTIALS_NAME = credentials('AWSCredentials')
  }

  stages {
    // Git Clone
    stage('Git Clone') {
      steps {
        git url: 'https://github.com/soliangpt/spring-petclinic.git/', branch: 'main'
      }
    }
    // Maven을 이용해 Build 한다.
    stage('Maven Build') {
      steps {
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
      post {
        success {
          echo 'Maven Build Success'
        }
        failure {
          echo 'Maven Build Failed'
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
           docker push soliangpt/spring-petclinic:latest
           """
      }
    }

    // Docker Image Remove
    stage('Docker Image Remove') {
      steps {
        echo 'Docker Image Remove'
        sh 'docker rmi -f spring-petclinic:$BUILD_NUMBER'
      }
    }

    // Upload to S3
    stage('upload to S3') {
      steps {
        echo 'Upload to S3'
        dir("$(env.WORKSPACE)") {
            sh 'zip -r scripts ./scripts appspec.yml'
            withAWS(region:"${REGION}", credentials:"${AWS_CREDENTIALS_NAME}") {
              s3Upload(file:"scripts.zip", bucket:"user07-codedeploy-bucket")
            }
            sh 'rm -rf ./scripts.zip'
        }
      }
    }


    // Code Deploy

    
  }  
}
