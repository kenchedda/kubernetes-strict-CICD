def label = "build"
podTemplate(label: label, yaml: """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: build
spec:
  containers:
  - name: build
    image: kenappiah/build-agent:2.0
    command:
    - cat
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
) {
    node (label) {

        stage ('Checkout SCM'){
          git url: 'https://github.com/kenchedda/kubernetes-strict-CICD.git', branch: 'main', credentialsId: 'github'
          container('build') {
                stage('Build a Maven project') {
                    sh 'mvn clean deploy -s settings.xml'
                    sh 'dockerd-entrypoint.sh'
                    sh 'until docker info; do sleep 1; done'
                                 
                }
            }
        }

        stage ('Docker Build'){
          container('build') {
                stage('Build Image') {
                    withCredentials([string(credentialsId: 'docker', variable: 'docker_hub_cred')]) {
                    def customImage = docker.build("kenappiah/webapp:1.0")
                    customImage.push()             
                    }   
                }
            }
        }

        stage ('Helm Chart') {
          container('build') {
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