pipeline {
    agent any
	tools {
		maven 'Maven'
  }	
    stages {
	    stage('Scm Checkout') {
		    steps {
			    checkout scm
		    }
	    }
	    stage('Build') {
		    steps {
			    sh 'mvn clean'
		    }
	    }
			    
	    stage('Test') {
		    steps {
			    echo "Testing..."
			    sh 'mvn test'
		    }
	    }
	    
	    stage('Build Docker Image') {
		    steps {
			    sh 'whoami'
			    sh """
				myimage = docker.build("fazilniveus/devops:${env.BUILD_ID}")

			    """	
			    
		    }
	    }
	    stage('Clair_scan'){
		    steps{
			    
			sh '''
				docker run -d --name db arminc/clair-db
				sleep 15 # wait for db to come up
				docker run -p 6060:6060 --link db:postgres -d --name clair arminc/clair-local-scan
				sleep 1
				DOCKER_GATEWAY=$(docker network inspect bridge --format "{{range .IPAM.Config}}{{.Gateway}}{{end}}")
				wget -qO clair-scanner https://github.com/arminc/clair-scanner/releases/download/v8/clair-scanner_linux_amd64 && chmod +x clair-scanner
				./clair-scanner --ip="$DOCKER_GATEWAY" fazilniveus/devops:${env.BUILD_ID} || exit 0
			'''
		    }
	    }
    }
}
