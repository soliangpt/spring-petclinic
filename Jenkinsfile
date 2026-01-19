pipeline {
  agent any

  tools {
    maven "M3"
    jdk "JDK17"
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
    }
    }

      

  }
}
