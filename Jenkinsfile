#!/usr/bin/env groovy
pipeline {

    environment {
        registry = "mathiamry1/jenkins-dev" 
        registryCredential = '7f27181a-6b78-4e40-b5e1-922101d4bf87' 
        dockerImage = '' 
    }
    agent any
    stages {

        stage('Cloning our Git') { 
            steps { 
                git url: 'http://gitlab.baamtu.com/mamadou.thiam/odoo-hello-world.git',
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
    }
 }
