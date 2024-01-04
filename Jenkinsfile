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
    args:
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
    
    stage('build with kaniko') {
        steps {
            container(name: 'kaniko', shell: '/busybox/sh'  ) {
              sh '''#!/busybox/sh
                echo "From jenkins/inbound-agent:latest: > Dockerfile
                /kaniko/executor --context 'pwd' --destination renum/test:v1.0
                '''
            }
        }
    }
  }
}