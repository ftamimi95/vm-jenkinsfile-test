script {
    settings = [
        "base": [
            'project': 'project-name',
            'region': 'us-east1',
            'zone': 'us-east1-b',
        ],
    ]
}

pipeline {
agent {
	node {
		label 'Worker'
		}
	}
  stages {
    stage('Authenticate to project') {
      steps {
        withCredentials([file(credentialsId: 'project-name/preferred to be project name', variable: 'FILE')]) {
              sh "gcloud auth activate-service-account --key-file=$FILE"
              sh "gcloud config set project ${settings['base'].project}"
              sh "gcloud config set compute/region ${settings['base'].region}"
              sh "gcloud config set compute/zone ${settings['base'].zone}"
            }
       }   
    
    }
    stage('Connect to VM'){
        steps{
            
            script{
                SSH_AGENT_ID = 'rainbow-dev'
                sshagent([SSH_AGENT_ID]) {
                            sh """ssh ftamimi@machine-ip -o StrictHostKeyChecking=no << EOF
			            echo switching to new directory
                            cd /var/www/html/some-project
                            pwd
                            sudo su
			            echo running grunt steps
                            grunt gcpdev
			            echo restarting servers
                            pm2 restart all
                            exit
                            exit
                            EOF"""

                    }
                }
            }
        }
    }
}
