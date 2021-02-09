pipeline {
    agent any
    stages {
      stage('Setup parameters') {
        steps {
          script { 
            properties([
              parameters([
                string(
                    defaultValue: '34.243.190.50', 
                    name: 'NGINX_DEV_IP', 
                    trim: true
                ),
                string(
                    defaultValue: '34.245.136.66', 
                    name: 'NGINX_PROD_IP', 
                    trim: true
                ),
                string(
                    defaultValue: 'insight-day-key', 
                    name: 'insight_day_key', 
                    trim: true
                )
              ])
            ])
          }
        }
      }
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
            sshagent(credentials: [params.insight_day_key]) { 
            sh 'ssh -vvv ubuntu@$NGINX_DEV_IP "pwd"'
            sh "rsync -e \"ssh -o StrictHostKeyChecking=no\" -r $WORKSPACE/sites/ ubuntu@$NGINX_DEV_IP:/usr/share/nginx/html/"
            }
	       }
      }

      stage ('Test') {
	      steps {
          sh """
            RESPONSE_CODE=\$(curl -o /dev/null -s -w "%{http_code}\n" https://$NGINGX_DEV_IP)
            if [[ "\$RESPONSE_CODE" != 200 ]]; then
                echo curl unssuccessful.  Expected response code 200, got "\$RESPONSE_CODE"
            fi
            """
	       }
      }

	    stage ('Deploy prod') {
	      steps {
          withCredentials(bindings:
            [
              sshUserPrivateKey(credentialsId: params.insight_day_key, keyFileVariable: 'SSH_KEY_PATH'),
            ]) {
            sh "rsync -e \"ssh -o StrictHostKeyChecking=no\" -r $WORKSPACE/sites/ -i \$SSH_KEY_PATH ubuntu@$NGINX_DEV_IP:/usr/share/nginx/html/"
          }
	      }
	    }
    }
}
