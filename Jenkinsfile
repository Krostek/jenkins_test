podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'git', image: 'alpine/git', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'awscli', image: 'amazon/aws-cli', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'docker', image: 'docker:dind', ttyEnabled: true, alwaysPullImage: true, privileged: true,
      command: 'dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2375 --storage-driver=overlay')
  ],
  volumes: [emptyDirVolume(memory: false, mountPath: '/var/lib/docker')]
  ) {
    node('mypod') {


	stage('Clone repository') {
            container('git') {
                sh 'git clone -b docker-build-test https://github.com/Krostek/jenkins_test.git'
            }
        }

	stage('AWS creds') {
            container('awscli') {
                sh 'aws ecr-public get-login-password --region us-east-1 > awscreds'
            }
        }
        
        stage('Check running containers') {
            container('docker') {
		sh 'cat awscreds | docker login --username AWS --password-stdin public.ecr.aws/u2i9x7e0'
		dir('jenkins_test') {
			sh 'pwd'
			sh 'docker build -t test:latest .'
			sh 'docker tag test:latest public.ecr.aws/u2i9x7e0/test:latest'
			sh 'docker push public.ecr.aws/u2i9x7e0/test:latest'
		}
            }
        }
        
    }
}
