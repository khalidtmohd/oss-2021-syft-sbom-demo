pipeline {
  agent any
  stages {
    stage('Checkout SCM') {
      steps {
        checkout scm
      }
    }

    stage('Build image and tag with build number') {
      steps {
        script {
          sh 'sudo docker build -t ${REPOSITORY}:${BUILD_NUMBER} .'
        }

      }
    }

    stage('Generate SBOM with syft') {
      steps {
        script {
          sh 'syft -o json ${REPOSITORY}:${BUILD_NUMBER} > sbom-${BUILD_NUMBER}.json'
        }

      }
    }

    stage('Generate vulnerability listing with grype') {
      steps {
        script {
          sh 'grype sbom:sbom-${BUILD_NUMBER}.json'
          // output vulns as json
          sh 'grype -o json sbom:sbom-${BUILD_NUMBER}.json > vulns-${BUILD_NUMBER}.json'
        }

      }
    }

    stage('Clean up') {
      steps {
        archiveArtifacts '*.json'
        sh '''
          docker rmi ${REPOSITORY}:${BUILD_NUMBER}
          rm *.json
          '''
      }
    }

  }
  environment {
    REPOSITORY = 'syft-sbom-demo'
  }
}