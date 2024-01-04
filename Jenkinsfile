pipeline {
  environment {
    registryCredential = "docker-credentials"
  }

    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:latest
    args: ["--dockerfile=Dockerfile",
           "--context=git://github.com/yooee/imagetest.git",
           "--destination=renum/test:v1.0"]
    volumeMounts:
      - name: kaniko-secret
        mountPath: /kaniko/.docker
  restartPolicy: Never
  volumes:
    - name: kaniko-secret
      secret:
        secretName: regcred
        items:
          - key: .dockerconfigjson
            path: config.json
"""
        }
    }
    

  stages {
    stage('Checkout') {
	      steps {
	            script {
	                git branch: 'main',
	                    credentialsId: 'github-credentials',
	                    url: 'https://github.com/yooee/imagetest.git'
	          }
        }
    }
    
    stage('build') {
        steps {
            container('kaniko') {
            sh '/kaniko/executor --context `pwd` \
               --destination docker.io/renum/test:v1.0 \
               --insecure \
               --skip-tls-verify  \
               --cleanup \
               --dockerfile Dockerfile \
               --verbosity debug'
            }
        }
    }
  }
}