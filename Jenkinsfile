pipeline {
  environment {
    PROJECT = "gj-playground"
    APP_NAME = "hipster-adservice"
    CLUSTER = "test-spinnaker"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/gj-playground/frontend-canary"
  }
  agent {
    kubernetes {
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
 name: kaniko
spec:
 containers:
 - name: kaniko
   image: gcr.io/kaniko-project/executor:debug
   command:
   - /busybox/cat
   tty: true
   volumeMounts:
     - name: kaniko-secret
       mountPath: /secret
   env:
     - name: GOOGLE_APPLICATION_CREDENTIALS
       value: /secret/kaniko-secret.json
 restartPolicy: Never
 volumes:
   - name: kaniko-secret
     secret:
       secretName: kaniko-secret
  
  """
}
  }
  stages {
    stage('Bake') {
      steps {
        container('kaniko') {
            sh '''
            pwd
            /kaniko/executor --dockerfile=./Dockerfile --context=/home/jenkins/agent/workspace/frontendcan --destination=gcr.io/gj-playground/frontend-canary --destination=gcr.io/gj-playground/frontend-canary 
            '''
      } 
   }
  
    }
    
  }
}
