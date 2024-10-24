pipeline {
     agent { label 'slave1' }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockercred')
	}
		
    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
                git 'https://github.com/madhusudhanrao25/star-agile-banking-finance.git'
            }
        }
        stage('Application Build') {
            steps {
                echo 'Perform Application Build'
                sh 'mvn clean package'
            }
        }
        stage('Docker Build') {
            steps {
                echo 'Perform Docker Build'
				sh "docker build -t maddy2964/banking-app:${BUILD_NUMBER} ."
				sh "docker tag maddy2964/banking-app:${BUILD_NUMBER} maddy2964/banking-app:latest"
            }
        }
        stage('Login to Dockerhub') {
            steps {
                echo 'Login to DockerHub'				
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                
            }
        }
        stage('Publish the Image to Dockerhub') {
            steps {
                echo 'Publish to DockerHub'
				sh "docker push maddy2964/banking-app:latest"                
            }
        }
        stage('Debug Workspace') {
            steps {
                echo 'Listing workspace files'
                sh 'ls -la /home/devopsadmin/workspace/Banking-App'
            }
        }
        stage('Deploy to Prod-Server') {
            steps {
                echo 'Running Ansible Playbook'
                ansiblePlaybook installation: 'ansible', 
                               inventory: '/etc/ansible/hosts',
                               playbook: '/home/devopsadmin/workspace/Banking-App/ansible-playbook.yml',
                               vaultTmpPath: ''
            }
        }
    }
}