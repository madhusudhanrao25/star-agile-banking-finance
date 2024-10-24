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
        stage('Copy Playbook to Master') {
            steps {
                echo 'Copying Ansible Playbook to Master Node'
                sh 'scp /home/devopsadmin/workspace/Banking-App/ansible-playbook.yml ansibleadmin@43.204.13.114:/tmp/'
            }
        }
        stage('Deploy to Prod-Server') {
            steps {
                echo 'Running Ansible Playbook on Master Node'
                sh '''
                    ssh ansibleadmin@43.204.13.114 "/usr/bin/ansible-playbook -i /etc/ansible/hosts /tmp/ansible-playbook.yml"
                '''
            }
        }   

    }
}