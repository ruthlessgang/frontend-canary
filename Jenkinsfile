pipeline {
  environment {
    PROJECT = "gj-playground"
    APP_NAME = "hipster-adservice"
    CLUSTER = "test-spinnaker"
    CLUSTER_ZONE = "us-central1-c"
    IMAGE_TAG = "gcr.io/gj-playground/frontend-canary"
    JENKINS_CRED = "gj-playground"
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
  volumes:
  - name: google-cloud-key
    secret: 
      secretName: jenkins-sa 
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/google.com/cloudsdktool/cloud-sdk:latest
    volumeMounts: 
    - name: google-cloud-key
      mountPath: /var/secrets/google
    env:
    - name: GOOGLE_APPLICATION_CREDENTIALS
      value: /var/secrets/google/key.json
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
            cat /var/secrets/google/key.json
            export GOOGLE_APPLICATION_CREDENTIALS=/var/secrets/google/key.json
            gcloud auth list
            '''
        }
      }
    }
    /** stage('Bake') {
      steps {
        container('kaniko') {
            sh '''
            pwd
            /kaniko/executor --dockerfile=./Dockerfile --context=/home/jenkins/agent/workspace/frontendcan --destination=gcr.io/gj-playground/frontend-canary --destination=gcr.io/gj-playground/frontend-canary 
            ''' **/
      } 
   }
