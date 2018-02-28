pipeline {
  agent none
  stages {
    stage('Clean Workspace') {
      agent any
      steps {
        sh 'git reset --hard'
      }
    }
    stage('Build App') {
      agent {
        docker {
          args '''-p 3000:3000
    -u root'''
          image 'node:8.9.3-alpine'
        }
        
      }
      steps {
        sh 'yarn install'
        sh 'yarn add image-webpack-loader'
        sh 'yarn build'
      }
    }
    stage('Containerize App') {
      agent any
      steps {
        sh 'docker build .'
      }
    }
    stage('Deploy to Azure') {
      agent {
        docker {
          image 'microsoft/azure-cli'
          args '-u root'
        }
        
      }
      steps {
        sh 'az --version'
      }
    }
    stage('az-dind') {
      agent {
        docker {
          image 'davevoyles/dind-azurecli'
          args '--privileged  -u root -v /var/run/docker.sock:/var/run/docker.sock '
        }
        
      }
      environment {
        ACRUSERNAME = credentials('ACRUSERNAME')
        ACRPASSWORD = credentials('ACRPASSWORD')
        ACRTENANT = credentials('ACRTENANT')      
      }
      steps {
        sh 'az --version'
        sh 'docker build --tag registry20171221043416.azurecr.io/code4clouds/react-boilerplate:1.0 .'
        sh 'az login --service-principal --username $ACRUSERNAME --password $ACRPASSWORD --tenant $ACRTENANT --allow-no-subscription'
        sh '''az acr login --name registry20171221043416
'''
        sh 'docker push registry20171221043416.azurecr.io/code4clouds/react-boilerplate:1.0'
      }
    }
  }
  environment {
    ACRUSERNAME = credentials('ACRUSERNAME')
    ACRPASSWORD = credentials('ACRPASSWORD')
    ACRTENANT = credentials('ACRTENANT')
    CI = 'true'
  }
}
