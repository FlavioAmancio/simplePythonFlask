podTemplate(containers: [
    containerTemplate(name: 'maven', image: 'maven:3.8.1-jdk-8', command: 'sleep', args:'99d'),
    containerTemplate(name: 'docker', image: 'docker:dind', command: 'sleep', args: '99d', ttyEnabled: true, prviliged: true)
  ],
  volumes: [ hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
){
    
    node(POD_LABEL)
    {
       container('docker'){
       
         stage("Clona Git") {
             git 'https://github.com/FlavioAmancio/simplepythonflask.git'
         }
    
         stage ("Build") {
             sh "docker build -t simple-python-flask:${BUILD_ID} ."
         }

         stage ("Teste") {
             sh "docker run -tdi --rm --name simple-python-flask-${BUILD_ID} --rm simple-python-flask:${BUILD_ID}"
             sh "docker exec simple-python-flask-${BUILD_ID} nosetests --with-xunit --with-coverage --cover-package=project test_users.py"
             sh "docker stop simlpe-python-flask-${BUILD_ID}"
             sh "docker tag simple-python-flask:${BUILD_ID} localhost:8082/simple-python-flask:${BUILD_ID}"
         }

         stage ("Push Image"){
             script {
                 docker.withRegistry('http://localhost:8082', 'jenkins_docker'){
                
                 sh 'docker push localhost:8082/simple-python-flask:${BUILD_ID}'
                 }
             }
         }
      }
    }
}