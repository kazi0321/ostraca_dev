def jenkins_path= "/var/lib/jenkins"
def tf_path = "${jenkins_path}/terraform"
def terraform = "/usr/local/bin/terraform"
def ansible_path = "${jenkins_path}/ansible"
def awk_op = "\$3"

node {

    stage('scm'){
        checkout scm
    }

    stage('terraform'){
        dir("${tf_path}"){
      sh "sh destroy.sh"
      sh "${terraform} apply -auto-approve -var \"target_arn =a\" "
      sh "echo [server1] > ${ansible_path}/hosts"
      sh "${terraform} state show aws_instance.server1 | grep public_dns | awk '{print ${awk_op}}' >> ${ansible_path}/hosts"
    }
  }

  stage('wget'){
      dir("${ansible_path}"){
    sh "rm -rf ostraca.yml"
    sh "rm -rf index.html"
    sh "wget https://raw.githubusercontent.com/kazi0321/ostraca_dev/master/playbook/ostraca.yml"
    sh "wget https://raw.githubusercontent.com/kazi0321/ostraca_dev/master/sources/index.html"
  }
  }

  stage('ansible'){
      dir("${ansible_path}"){
    sh "ansible-playbook -i hosts ostraca.yml --private-key=~/.ssh/kaziken.pem"
  }
  }


  stage('terraform'){
        dir("${tf_path}"){
        sh "${terraform} apply -auto-approve -var \"target_arn =b\" "
    }
  }
}
