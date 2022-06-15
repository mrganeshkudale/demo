pipeline {
    agent any
    environment{
		IMAGE_NAME="localmachine/application:${BUILD_NUMBER}"
	}
    stages {
        stage('application-build') {
            steps {
		sh "cd ~ && rm -rf Python && git clone https://github.com/mrganeshkudale/Python.git"
            }
	}
	stage('application-containerization'){
	    steps {
		sh "cd ~/Python && sudo -S docker build --build-arg DEPENDENCY=build/dependency -t localpy${BUILD_NUMBER} ."                
	    }
	}
	stage('image-versioning'){
	    steps {
		sh "sudo -S docker run -d -p 5000:5000 --restart=always --name registry registry:2"
		sh "sudo -S docker tag localpy${BUILD_NUMBER} localhost:5000/mrganeshkudale/localpy${BUILD_NUMBER}"
		sh "sudo -S docker push localhost:5000/mrganeshkudale/localpy${BUILD_NUMBER}"
	    }
	}
	stage('deploy-cloud'){
	    steps{
		sh "sudo -S kubectl create deployment localpydeployment${BUILD_NUMBER} --image=localhost:5000/mrganeshkudale/localpy${BUILD_NUMBER}"
		sh "sudo -S kubectl expose deployment localpydeployment${BUILD_NUMBER} --type=LoadBalancer --port=3333 --name=localpyservice${BUILD_NUMBER}"
	    }
	}
    }
}
