pipeline {
    agent any 
    tools { 
        maven 'Maven' 
      
    }
stages { 
     
 stage('checkout') { 
     steps {
// for display purpose

      // Get some code from a GitHub repository

        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/shivanani220/game-of-life.git']]])      

      // Get the Maven tool.
     
 // ** NOTE: This 'M3' Maven tool must be configured
 
     // **       in the global configuration.   
     }
   }

   stage('Build') {
       steps {
       // Run the maven build

      //if (isUnix()) {
         sh 'mvn -Dmaven.test.failure.ignore=true install'
      //} 
      //else {
      //   bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
       }
//}
   }
 
  stage('Unit Test Results') {
      steps {
      junit '**/target/surefire-reports/TEST-*.xml'
      
     }
 }
 stage('sonarqube') {
    environment {
        def scannerHome = tool 'sonarqube';
    }
    steps {
      withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 10, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
    }
}
     stage('Artifact upload') {
      steps {
	  nexusArtifactUploader credentialsId: 'nexus', groupId: 'CI_CD', nexusUrl: 'http://3.133.98.64:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'java', version: '$BUILD_NUMBER'
      }
     }
    stage('Deploy War') {
      steps {
        sh label: '', script: 'ansible-playbook deploy.yml'
      }
 }
}
post {
       success {
            archiveArtifacts 'gameoflife-web/target/*.war'
        }
       failure {
           mail bcc: '', body: '', cc: '', from: '', replyTo: '', subject: '', to: 'shivavamshi..89@gmail.com'
        }
    }       
}
