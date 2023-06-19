pipeline {
    
    agent {
        label "build"
    }


    
    stages {

                stage ('Checkout SCM'){
                  steps {
                git url: 'https://github.com/kenchedda/kubernetes-strict-CICD.git', branch: 'main', credentialsId: 'github'
                }
                }


                stage('Build a Maven project') {
                  steps {
                    sh 'mvn clean deploy -s settings.xml'                 
                }
                }         
        

             
          
                stage('Build Image') {
                  steps {
                    script {
                    docker.withRegistry( 'https://registry.hub.docker.com', 'docker' ) {
                    def customImage = docker.build("kenappiah/webapp")
                    customImage.push()             
                    }
                }
                }
                }
            
        
            
        

        stage ('Helm Chart') {
          steps {
          
            dir('charts') {
              withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
              sh '/usr/local/bin/helm package webapp'
              sh '/usr/local/bin/helm push-artifactory webapp-1.0.tgz https://edwikifacts.jfrog.io/artifactory/edweb-helm-local --username $username --password $password'
              }
            }
          }
        }
    }
}