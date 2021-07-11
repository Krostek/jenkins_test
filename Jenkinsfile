podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'git', image: 'alpine/git', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'docker', image: 'docker:dind', ttyEnabled: true, alwaysPullImage: true, privileged: true,
      command: 'dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2375 --storage-driver=overlay')
  ],
  volumes: [emptyDirVolume(memory: false, mountPath: '/var/lib/docker')]
  ) {
    node('mypod') {


	stage('Clone repository') {
            container('git') {
                sh 'whoami'
                sh 'hostname -i'
                sh 'git clone -b docker-build-test https://github.com/Krostek/jenkins_test.git'
            }
        }
        
        stage('Check running containers') {
            container('docker') {
		dir('jenkins_test') {
			sh 'pwd'
			sh 'docker build -t test:latest .'
		}
            }
        }
        
    }
}
