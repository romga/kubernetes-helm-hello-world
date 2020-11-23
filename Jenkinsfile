def prepareK8sTools(){
    sh '''#!/bin/bash -e
        mkdir .bin && cd .bin \
        && curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.16.13/bin/linux/amd64/kubectl \
        && chmod +x kubectl \
        && curl -LO https://get.helm.sh/helm-v3.4.0-rc.1-linux-amd64.tar.gz \
        && tar -xvf helm-v3.4.0-rc.1-linux-amd64.tar.gz \
        && mv linux-amd64/helm . \
        && rm -rf helm-v3.4.0-rc.1-linux-amd64.tar.gz \
        && chmod +x helm \
        && rm -rf linux-amd64/ \
        && pip install awscli \
        && pip install ansible
'''
}

pipeline{
    agent any
    stages {
        stage('prepare') {
            steps {
                prepareK8sTools()
            }
        }
        stage('deploy') {
            steps {
                withAWS(credentials: "aws", region: "eu-west-1") {
                    sh '''#!/bin/bash -e
                    export PATH=.bin:/home/jenkins/.local/bin:$PATH
                    aws eks update-kubeconfig --name sndbx  --alias sndbx 
                    ansible-galaxy collection install community.kubernetes
                    ansible-playbook playbook.yml
                    '''


                }


            }
        }
    }
}
