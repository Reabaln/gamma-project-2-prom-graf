pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    service_name: department-service
    service_type: REST
spec:
  containers:
  - name: dnd
    image: docker:latest
    command: 
    - cat
    tty: true
    volumeMounts: 
    - mountPath: /var/run/docker.sock
      name: docker-sock
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock  
      type: Socket
"""
    }
  }
  environment {
    CREDS = credentials('creds-reab')
    DOCKER_NAMESPACE = 'reabaln'
    SERVICE_NAME = 'pro-graf-service'
    TOKEN=credentials('5c169eaa-8db2-4f65-bea0-a9dd8b9f84c6')
  }
  stages {
      stage("Install") {
          steps {
              container('dnd') {
                  sh '''
			helm repo add stable https://kubernetes-charts.storage.googleapis.com
			helm repo update
			kubectl create namespace monitor
			helm install prometheus-operator stable/prometheus-operator --namespace monitor --set grafana.service.type=LoadBalancer
			kubectl apply -f ingress.yaml -n monitor

                  '''
              }
          }
      }
}
}
