pipeline {
    agent any
    
    environment {
        NGINX_DEV_IP = '34.245.91.68'
        NGINX_PROD_IP = '52.210.134.244'
        insight_day_key_name = 'insight-day-key'
    }

    stages {
      stage('Cleanup Workspace') {
        steps {
              cleanWs()
              sh """
              echo "Cleaned Up Workspace For Project"
              """
        }
      }

      stage('Code Checkout') {
        steps {
          checkout scm
        }
      }

	    stage ('Deploy dev') {
	      steps {
          sshagent(credentials: [env.insight_day_key]) {
            sh """
            rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/insightday ubuntu@$NGINX_DEV_IP:/usr/share/nginx/html/insightday
            """
	        }
        }
      }

      stage ('Test') {
	      steps {
          sh """
            RESPONSE_CODE=\$(curl -o /dev/null -s -w "%{http_code}\n" http://$NGINX_DEV_IP)
            if [ "\$RESPONSE_CODE" != 200 ]; then
                echo curl unsuccessful.  Expected response code 200, got "\$RESPONSE_CODE"
            fi
            """
	       }
      }

	    stage ('Deploy prod') {
	      steps {
          sshagent(credentials: [env.insight_day_key]) {
            sh """
            rsync -e "ssh -o StrictHostKeyChecking=no" -r $WORKSPACE/sites/insightday ubuntu@$NGINX_PROD_IP:/usr/share/nginx/html/insightday
            """
	        }
	      }
	    }
    }
}
