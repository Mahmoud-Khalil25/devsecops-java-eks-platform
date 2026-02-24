pipeline {
  agent { label 'jenkins-slave' }

  tools {
    maven 'maven3'
    jdk 'java17'
  }

  stages {

    stage('Cleanup Workspace') {
      steps {
        cleanWs()
      }
    }

    stage('Checkout from SCM') {
      steps {
        git branch: 'main',
            credentialsId: 'github-creds',
            url: 'https://github.com/Mahmoud-Khalil25/devsecops-java-eks-platform.git'
      }
    }

    stage('Compilation') {
      steps {
        sh 'mvn -B clean compile'
      }
    }

    stage('Unit Testing') {
      steps {
        sh 'mvn -B test'
      }
    }

    stage('File System Scan') {
      steps {
        sh '''
          trivy fs \
            --severity HIGH,CRITICAL \
            --scanners vuln,secret,config \
            --format template \
            --template "@/usr/share/trivy/templates/html.tpl" \
            --output trivy-fs-report.html \
            --no-progress \
            .
        '''
        archiveArtifacts artifacts: 'trivy-fs-report.html', fingerprint: true
      }
    }

    stage('Build Application') {
      steps {
        sh 'mvn -B clean package'
      }
    }

  
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('sonar-server') {
          sh 'mvn -B sonar:sonar'
        }
      }
    }

  
    stage('Quality Gate') {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: false
        }
      }
    }

  }
}
