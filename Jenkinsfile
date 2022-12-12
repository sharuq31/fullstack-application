
    pipeline {
    agent any
    environment {
        PATH = "/usr/share/maven/bin:$PATH"
    }
    stages {
        stage('Git') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'Github', url: 'https://github.com/kishorepv909/minikube_level1.git']]])
            }
        }
     stage ('maven build'){
            steps{
            dir("spring-boot-student-app-api"){
                sh 'mvn clean install'
                }
            }
        }

     stage('Parallel Stage') {
            parallel {
    stage ('build_dockerfile-1') {
        steps {
            sh "pwd"
            dir('react-student-management-web-app'){
               sh " docker build -t kishore ."  
            }
        }
    }
     stage ('build_dockerfile-2') {
        steps {
            dir("spring-boot-student-app-api"){
             sh " docker build -t kishore1 ."   
            }
        }
    }
  }
     }
  stage ('login & deploy in k8s') {
        steps {
         sshagent(['k8s']) {
            dir("k8s") {
        sh "pwd"
        sh "ls -a"
             }
    sh 'scp -r  */*.yaml ubuntu@52.39.114.165:/home/ubuntu'
    sh "ssh ubuntu@52.39.114.165 kubectl apply -f /home/ubuntu "
         }    
      }
     }
     stage ("docker Push") {
         steps {
             withCredentials([string(credentialsId: 'docker_hub', variable: 'docker_hub')]) {
             sh "docker login -u kishorepv909 -p ${docker_hub}"
                 
             sh "docker tag kishore kishorepv909/front_end_app:kishore"
               sh "docker push kishorepv909/front_end_app:kishore"
               
               sh "docker tag kishore1 kishorepv909/front_end_app:kishore1"
               sh "docker push kishorepv909/front_end_app:kishore1"
                 
             } 
         }
     }
     
   }
 }
