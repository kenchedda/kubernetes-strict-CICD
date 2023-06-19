pipeline {
    
    agent {
        label "build"
    }


    
    stages {

                stage ('Checkout SCM'){
                git url: 'https://github.com/kenchedda/kubernetes-strict-CICD.git', branch: 'main', credentialsId: 'github'
                }


                stage('Build a Maven project') {
                    sh 'mvn clean deploy -s settings.xml'                 
                }
            
        

          
                stage('Build Image') {
                    withCredentials([string(credentialsId: 'docker', variable: 'docker_hub_cred')]) {
                    def customImage = docker.build("kenappiah/webapp:1.0")
                    customImage.push()             
                    }   
                }
            
        

        stage ('Helm Chart') {
          
            dir('charts') {
              withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
              sh '/usr/local/bin/helm package webapp'
              sh '/usr/local/bin/helm push-artifactory webapp-1.0.tgz https://edwikifacts.jfrog.io/artifactory/edweb-helm-local --username $username --password $password'
              }
            }
        
        }
    }
}