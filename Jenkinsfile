pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'Java17'
    }

    stages {

       stage('SCM GitHub') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mimaraslan/devops-02-pipeline']])
            }
        }

        stage('Test Maven') {
            steps {
            //    sh 'mvn test'
                bat 'mvn test'
            }
        }


        stage('Build Maven') {
            steps {
            //    sh 'mvn clean install'
                bat 'mvn clean install'
            }
        }


        stage('Docker Image') {
            steps {
            //    sh 'docker build  -t mimaraslan/devops-application:latest   .'
                bat 'docker build  -t mimaraslan/devops-application:latest   .'
            }
        }


        stage('Docker Image To DockerHub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {

                        if (isUnix()) {
                             sh 'docker login -u mimaraslan -p %dockerhub%'
                             sh 'docker push mimaraslan/devops-application:latest'
                          } else {
                             bat 'docker login -u mimaraslan -p %dockerhub%'
                             bat 'docker push mimaraslan/devops-application:latest'
                         }
                    }
                }
            }
        }
    

        stage('Deploy Kubernetes') {
            steps {
            script {
                    kubernetesDeploy (configs: 'deployment-service.yaml', kubeconfigId: 'kubernetes')
                }
            }
        }


        stage('Docker Image to Clean') {
            steps {
              
                   //  sh 'docker image prune -f'
                     bat 'docker image prune -f'
               
            }
        }


    }

}