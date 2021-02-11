pipeline {
    agent any
    
    environment {
        NGINX_DEV_IP = '34.245.91.68'
        NGINX_PROD_IP = '52.210.134.244'
        insight_day_key_name = 'insight-day-key'
    }

    stages {
        // Remove any existing configuration present in Jenkins.  You can ignore this bit
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

        // Ensures that jenkins has the most up-to-date code for this branch.  Ignore this too
        stage('Code Checkout') {
            steps {
              checkout scm
            }
        }
    
        // rsync ensures that the files present in a source directory are also present in a target directory
        // In this example the source is on the jenkins machine and the destination is the $NGINX_DEV_IP environment
        // variable which is defined at the top of the file.
        
        // In the pipeline, Jenkins has cloned this repository, so imagine that $WORKSPACE/sites/insightday/ is the same
        // as sites/insightday/ in this repository

        // Where does the NGINX_DEV_IP environment varialbe come from?
        stage ('Deploy dev') {
    	    steps {
                sshagent(credentials: [env.insight_day_key]) {
                    sh """
                    rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/insightday/ ubuntu@$NGINX_DEV_IP:/usr/share/nginx/html/insightday
                    """
    	        }
            }
        }
    
    	    }
        }
    
    	stage ('Deploy prod') {
            steps {
                sshagent(credentials: [env.insight_day_key]) {
                    sh """
                    rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/insightday/ ubuntu@$NGINX_PROD_IP:/usr/share/nginx/html/insightday
                    """
    	        }
    	    }
    	}
    }
}
