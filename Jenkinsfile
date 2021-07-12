#!/usr/bin/env groovy
def COLOR_MAP = [
        'SUCCESS': 'good',
        'FAILURE': 'danger',
    ]
pipeline {

    environment {
        doError = '0'
        registry = "mathiamry1/jenkins-dev" 
        registryCredential = '243e4d4d-a961-496d-858b-3bc5f65e40e1' 
        dockerImage = '' 
    }
    agent any
    stages {
          
         stage('Checkout code') {
                steps {
                    checkout scm
                }
            }
        stage('Test') {
        steps {
            script {
                println "${env.BRANCH_NAME}"
            }   
        }
      } 
        stage('Cloning our Git') { 
            steps { 
                sh "echo ${env.BRANCH_NAME}"
                git url: 'https://github.com/mathiamry/jenkins-dev.git',
                credentialsId: registryCredential
            }
        } 
        
        stage('Building our image') { 
            steps { 
                script { 
                    def dockerHome = tool 'docker'
                    env.PATH = "${dockerHome}/bin:${env.PATH}"
                    dockerImage = docker.build registry + ":$BUILD_NUMBER" 
                }
            } 
        }
        stage('Deploy our image') { 
            steps { 
                script { 
                    docker.withRegistry( '', '4b06d6c8-486f-4a42-8cd7-d0cb76e5b00d' ) { 
                        dockerImage.push() 
                    }
                } 
            }
        } 
        stage('Cleaning up') { 
            steps { 
                sh "docker rmi $registry:$BUILD_NUMBER" 
            }
        } 
        stage('Error') {
      // when doError is equal to 1, return an error
      when {
        expression { doError == '1' }
      }
      steps {
        echo "Failure :("
        error "Test failed on purpose, doError == str(1)"
      }
    }
    stage('Success') {
      // when doError is equal to 0, just print a simple message
      when {
        expression { doError == '0' }
      }
      steps {
        echo "Success :)"
      }
    }

  }

  // Post-build actions
  post {
    success {
      slackSend channel: '#jenkins-ci-dev',
      color: COLOR_MAP[currentBuild.currentResult],
      message: "*${currentBuild.currentResult}:* JOB ${env.JOB_NAME} | BUILD N° = ${env.BUILD_NUMBER}\n Plus d'infos: ${env.BUILD_URL} \n Une nouvelle image est disponible \n Message du commit : https://github.com/mathiamry/jenkins-dev/commit/master} \n Lien du commit:https://github.com/mathiamry/jenkins-dev/commit/master "
    }
    failure {
      slackSend channel: '#jenkins-ci-dev',
      color: COLOR_MAP[currentBuild.currentResult],
      message: "*${currentBuild.currentResult}:* JOB ${env.JOB_NAME} | BUILD N° = ${env.BUILD_NUMBER}\n Plus d'infos: ${env.BUILD_URL} \n Message du commit : ${env.GIT_COMMIT} \n Lien du commit: https://github.com/mathiamry/jenkins-dev/commit/master"
    }
  }
    }
