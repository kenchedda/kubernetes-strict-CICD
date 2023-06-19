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
        

          
                stage('Docker image build'){
                    steps {
                        script {
                            sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                            sh 'docker image tag $JOB_NAME:v1.$BUILD_ID kenappiah/$JOB_NAME:latest'
                        }
                    }
            
                }
                stage ('publish docker image') {
                steps{
                    script{
                        withCredentials([string(credentialsId: 'docker', variable: 'docker_hub_cred')]) {
                            sh 'docker login -u kenappiah -p ${docker_hub_cred}'
                            sh 'docker image push kenappiah/$JOB_NAME:latest'
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