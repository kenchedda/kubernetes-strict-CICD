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
                    withCredentials([string(credentialsId: 'docker', variable: 'docker_hub_cred')]) {
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
              sh 'curl -ujnrcheddabob@gmail.com:Widaad@77 -T /home/ec2-user/workspace/job/charts/webapp-1.0.tgz "https://kenappiah.jfrog.io/artifactory/default-helm/webapp-1.0.tgz"'
              
          }
        }
    }
}
    }
}