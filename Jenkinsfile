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
      // Docker Image 생성
      stage('Docker Image Build') {
        steps {
          echo 'Docker Image Build'
          
        }
      }
      // Docker Image Upload
      stage('Docker Image Build') {
        steps {
          echo 'Docker Image Build'

        }
      }

      // Target 로 *.jar 전송
      stage('SSH Publish') {
        steps { 
          echo 'SSH Publish'
          sshPublisher(publishers: [sshPublisherDesc(configName: 'target',
          transfers: [sshTransfer(cleanRemote: false,
          excludes: '',
          execCommand: '''
          fuser -k 8080/tcp
          export BUILD_ID=Pipeline-PetClinic
          nohup java -jar /home/ubuntu/spring-petclinic-4.0.0-SNAPSHOP.jar >> nohup.out 2>&1 &''',
          execTimeout: 120000,
          flatten: false,
          makeEmptyDirs: false,
          noDefaultExcludes: false,
          patternSeparator: '[, ]+',
          remoteDirectory: '',
          remoteDirectorySDF: false,
          removePrefix: 'target',
          sourceFiles: 'target/*.jar')],
          usePromotionTimestamp: false,
          useWorkspaceInPromotion: false,
          verbose: false)])
          
        }
      }
    
  }
}
