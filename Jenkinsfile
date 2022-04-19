pipeline {
  environment {
    PROJECT = "gj-playground"
    IMAGE_TAG = "gcr.io/gj-playground/frontend-canary"
    SVC_ACCOUNT_KEY = credentials('jenkins-sa')
  }
  agent {
    kubernetes {
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
labels:
  component: ci
spec:
  restartPolicy: Never
  containers:
  - name: gcloud
    image: gcr.io/google.com/cloudsdktool/cloud-sdk:latest
  volumes:
  - name: jenkins-sa
    secret:
      secretName: jenkins-sa
    volumeMounts:
    - name: jenkins-sa
      mountPath: /secret
    env:
    - name: GOOGLE_APPLICATION_CREDENTIALS
      value: /secret/kaniko-secret.json
    command:
    - cat
    tty: true
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - cat
    tty: true
  """
}
  }
  stages {
    stage('test') {
      steps {
        container('gcloud') {
            sh '''
            echo -n $SVC_ACCOUNT_KEY > "${HOME}/serviceaccount.json"
            cat ${HOME}/serviceaccount.json
            gcloud auth activate-service-account --key-file="${HOME}/serviceaccount.json"
            gcloud auth list
            '''
        }
      }
      
      }
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
