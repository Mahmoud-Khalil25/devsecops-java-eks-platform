pipeline {
  agent { label 'jenkins-slave' }

  tools {
    maven 'maven3'
    jdk 'java17'
  }
  
environment {
  AWS_REGION = 'eu-west-3'          
  ECR_REPO   = 'devsecops-java-app'    
  IMAGE_TAG  = "${env.BUILD_NUMBER}"  
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

stage('Build Docker Image') {
  steps {
    sh '''
      set -e
      AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
      ECR_URI="$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO"

      echo "Building image: $ECR_URI:$IMAGE_TAG"
      docker build -t "$ECR_URI:$IMAGE_TAG" .
      docker tag "$ECR_URI:$IMAGE_TAG" "$ECR_URI:latest"
    '''
  }
}

stage('Trivy Image Scan') {
  steps {
    sh '''
      set +e
      AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
      ECR_URI="$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO"

      trivy image \
        --severity HIGH,CRITICAL \
        --format template \
        --template "@/usr/share/trivy/templates/html.tpl" \
        --output trivy-image-report.html \
        --no-progress \
        "$ECR_URI:$IMAGE_TAG"

      exit 0
    '''
    archiveArtifacts artifacts: 'trivy-image-report.html', fingerprint: true
  }
}

stage('Push Image to ECR') {
  steps {
    sh '''
      set -e
      AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
      ECR_URI="$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO"

      aws ecr get-login-password --region "$AWS_REGION" | \
        docker login --username AWS --password-stdin \
        "$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com"

      docker push "$ECR_URI:$IMAGE_TAG"
      docker push "$ECR_URI:latest"
    '''
  }
}






    

  }
}
