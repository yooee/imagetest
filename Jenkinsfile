pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins/agent-type: kaniko
spec:
  containers:
    - name: kaniko
      image: gcr.io/kaniko-project/executor:debug
      volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker/
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

    environment {
        DOCKERHUB_USERNAME = "renum"
        IMAGE_NAME = "test"
    }

    stages {
        stage("Build Docker Image & Push to Docker Hub") {
            steps {
                container("kaniko") {
                    script {
                        def context = "."
                        def dockerfile = "Dockerfile"
                        def image = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}:latest"

                        sh "/kaniko/executor --context ${context} --dockerfile ${dockerfile} --destination ${image}"
                    }
                }
            }
        }
    }
    post {
        always {
            echo "The process is completed."
        }
    }
}
