pipeline {
  agent { label 'aws-devops' }
  options {
    disableConcurrentBuilds()
    timeout(time: 20, unit: 'MINUTES')
    skipDefaultCheckout(true)
  }
  environment {
    ANSIBLE_CONFIG = "${WORKSPACE}/ansible/ansible.cfg"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Validate Ansible') {
      steps { sh "ansible-playbook --syntax-check -i 'localhost,' ansible/deploy.yml" }
    }
    stage('Deploy application') {
      when { branch 'main' }
      steps {
        withCredentials([file(credentialsId: 'app-inventory', variable: 'APP_INVENTORY')]) {
          sshagent(credentials: ['ec2-deploy-key']) {
            sh 'ansible-playbook -i "$APP_INVENTORY" ansible/deploy.yml'
          }
        }
      }
    }
  }
}
