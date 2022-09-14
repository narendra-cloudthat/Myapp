pipeline {
    environment {
        SERVICE_NAME="terraform"
        NAMESPACE="test"
        HELM_BRANCH="qa1"

    }
    agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          annotations:
            eks.amazonaws.com/role-arn: arn:aws:iam::992022947233:role/Jenkins-slave-non-prod-eks1
          labels:
            some-label: some-label-value
        spec:
          serviceAccountName: jenkins-slave
          containers:
          - name: master
            image: 992022947233.dkr.ecr.ap-south-1.amazonaws.com/jenkins-dnd-base:v2
            command: ['sleep' , '5000']
            volumeMounts:
            - mountPath: /var/run/docker.sock
              name: test-volume
          volumes:
          - name: test-volume
            hostPath:
              path: /var/run/docker.sock
        '''
    }
  }
    stages {
        stage("Git Repo checkout"){
          steps{
            container('master') {
              sh("echo ${tag}")
              checkout([$class: 'GitSCM', branches: [[name: 'refs/tags/${tags}']], extensions: [], userRemoteConfigs: [[credentialsId: 'Git_Pass', url: 'https://github.com/narendra-cloudthat/Myapp.git']]])
            }
          }   
        }
        stage('Git Check') {
            steps {
                container('master') {
                    withCredentials([gitUsernamePassword(credentialsId: 'Git_Pass', gitToolName: 'Default')]) {
                    sh '''
                        cd $WORKSPACE
                        ls
                        echo ${tags}
                    '''
                    }
                }
            }
        }
    }
}