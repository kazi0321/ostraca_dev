def jenkins_path= "/var/lib/jenkins"
def tf_path = "${jenkins_path}/terraform"
def terraform = "/usr/local/bin/terraform"
def ansible_path = "${jenkins_path}/ansible"

node {

    stage('scm'){
        checkout scm
    }

    stage('terraform'){
        dir("${tf_path}"){
      sh "sh destroy.sh"
      sh "${terraform} apply -auto-approve -var target_arn =a "
      sh "echo [server1]" > ${ansible_path}/hosts"
      sh "terraform state show aws_instance.server1 | grep public_dns | awk '{print $3}' >> ${ansible_path}/hosts"
    }
  }

  stage('ansible'){
      dir("${ansible_path}"){
    sh "rm -rf ostraca.yml"
    sh "wget https://github.com/kazi0321/test/ostraca_dev/master/playbook/ostraca.yml"
    sh "ansible-playbook -i hosts ostraca.yml --private-key=~/.ssh/kaziken.pem"
  }
}

  stage('terraform'){
        dir("${tf_path}"){
        sh "${terraform} apply -auto-approve -var target_arn =b "
    }
  }

}