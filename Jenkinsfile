pipeline{agent{
        kubernetes{
            yaml '''
            apiVersion: v1
            kind: Pod
            metadata:
              labels:
                jenkins/kube-default: true
                app: jenkins
                component: agent
            spec:
              containers:
                - name: maven
                  image: maven:alpine
                  command:
                  - cat
                  tty: true
                  imagePullPolicy: Always
                  env:
                  - name: POD_IP
                    valueFrom:
                      fieldRef:
                       fieldPath: status.podIP
                  - name: DOCKER_HOST
                    value: tcp://localhost:2375
                - name: docker
                  image: docker:dind
                  securityContext:
                    privileged: true
                  volumeMounts:
                    - name: dind-storage
                      mountPath: /var/lib/docker
              volumes:
                - name: dind-storage
                  emptyDir: {}
    
            '''
        }
    }

     stages{
        stage('Clone'){
           steps {
 container('maven') {
 git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Abvirk/JenkinsWebApi.git'
 }
 }
        }
    stage('Build-Docker-Image') {
 steps {
 container('docker') {
 sh 'docker build -t abvirk/jenkinswebapi:1 .'
 }
 }
 }
        stage('Docker Buil'){
            steps {
 container('docker') {
 sh 'docker login -u abvirk -p dckr_pat_zx_8UEdcLTOhc-BPvU-1T1zwD2E'
 }
 }
        }
        stage('Docker Push'){
             steps {
 container('docker') {
 sh 'docker push abvirk/jenkinswebapi:1'
 }
 }
        }
     }
      post {
 always {
 container('docker') {
 sh 'docker logout'
 }
 }
 }      
}