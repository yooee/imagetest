pipeline {
    agent {
        kubernetes {
            defaultContainer 'kaniko'
            yaml '''
kind: Pod
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - sleep
    args:
    - 99d
    volumeMounts:
      - name: kaniko-secret
        mountPath: /kaniko/.docker
  volumes:
    - name: kaniko-secret
      secret:
        secretName: regcred
        items:
          - key: .dockerconfigjson
            path: config.json
'''
        }
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                    env.GIT_TAG = sh(script: "git describe --tags --always", returnStdout: true).trim()
                }
            }
        }
        stage('Build and Push Image') {
            steps {
                container('kaniko') {
                    script {
                        sh '''
                        /kaniko/executor --context git://github.com/yooee/imagetest.git --dockerfile=Dockerfile --destination=renum/test:${env.GIT_TAG}
                        '''
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Image build and push to the registry has been completed successfully.'
        }
    }
}

