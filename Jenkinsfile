pipeline {
    agent any
    
    environment {
        NGINX_DEV_IP = '34.245.91.68'
        NGINX_PROD_IP = '52.210.134.244'
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
                sshagent(credentials: ['insight-day-key']) {
                    sh """
                    rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/insightday/ ubuntu@$NGINX_DEV_IP:/usr/share/nginx/html/insightday
                    """
    	        }
            }
        }
    
        // Test that the server is still working.  What is happening here?
        // curl is a command line tool to make requests to urls
        stage ('Test dev') {
    	    steps {
                sh """
                RESPONSE_CODE=\$(curl -o /dev/null -s -w "%{http_code}\n" http://$NGINX_DEV_IP)
                if [ "\$RESPONSE_CODE" != 201 ]; then
                    echo curl unsuccessful.  Expected response code 200, got "\$RESPONSE_CODE"
                    exit 2
                fi
                """
    	    }
        }
    
    	stage ('Deploy prod') {
            when {
                // We only want to deploy code to production that has been merged to the main branch
                branch 'main'
            }
    	    steps {
                sshagent(credentials: ['insight-day-key']) {
                    sh """
                    rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/insightday/ ubuntu@$NGINX_DEV_IP:/usr/share/nginx/html/insightday
                    """
    	        }
    	    }
    	}
    }
}
