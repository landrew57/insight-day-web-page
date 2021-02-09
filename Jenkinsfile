pipeline {
    agent {
<<<<<<< HEAD
      	any
=======
	any
>>>>>>> First commit initial setup
    }
    stages {
      stage('Setup parameters') {
        steps {
<<<<<<< HEAD
          script { 
            properties([
              parameters([
                string(
                    defaultValue: '', 
                    name: 'NGINX_DEV_IP', 
                    trim: true
                ),
                string(
                    defaultValue: '', 
                    name: 'NGINX_PROD_IP', 
                    trim: true
                ),
                string(
                    defaultValue: 'SSH_KEY_VARIABLE', 
                    name: 'insight_day_key', 
=======
          script {
            properties([
              parameters([
                string(
                    defaultValue: '',
                    name: 'NGINX_DEV_IP',
                    trim: true
                ),
                string(
                    defaultValue: '',
                    name: 'NGINX_PROD_IP',
                    trim: true
                ),
                string(
                    defaultValue: 'SSH_KEY_VARIABLE',
                    name: 'insight_day_key',
>>>>>>> First commit initial setup
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
        checkout scm
      }

	    stage ('Deploy dev') {
	      steps {
          withCredentials(bindings:
            [
              sshUserPrivateKey(credentialsId: params.insight_day_key, keyFileVariable: 'SSH_KEY_PATH')
            ])
          sh "rsync -r $WORKSPACE/sites/ -i $SSH_KEY_PATH ubuntu@$NGINX_IP:/usr/share/nginx/html/"
	       }
      }

      stage ('Test') {
	      steps {
          sh """
            RESPONSE_CODE=$(curl -o /dev/null -s -w "%{http_code}\n" https://$NGINGX_DEV_IP)
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
            ])
          sh "rsync -r $WORKSPACE/sites/ -i $SSH_KEY_PATH ubuntu@$NGINX_IP:/usr/share/nginx/html/"
	       }
	     }
    }
}
