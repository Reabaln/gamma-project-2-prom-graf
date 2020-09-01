pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  - name: helm
    image: alpine/helm
    command:
    - cat
    tty: true
"""
    }
  }
  environment {
    TOKEN=credentials('cffc1cc9-775e-4507-86b8-54433e85e3ae')
  }
	
  stages {

      stage("Deploy") {
          steps {
              container('kubectl') {
                  sh '''
                      kubectl --token=$TOKEN create namespace monitor
                  '''
              }
          }
      }

      stage("Install") {
          steps {
              container('helm') {
                  sh '''
			helm repo add stable https://kubernetes-charts.storage.googleapis.com
			helm repo update
			helm install prometheus-operator stable/prometheus-operator --namespace monitor --set grafana.service.type=NodePort --set prometheusOperator.admissionWebhooks.enabled=false --set prometheusOperator.admissionWebhooks.patch.enabled=false --set prometheusOperator.tlsProxy.enabled=false --values toleration.yaml
                  '''
              }
          }
      }
	  
}
}

