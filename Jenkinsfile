def get_tag() {
  sh (
     script: "curl -s http://ms-counter.ms-counter.k8s1.labo.local/get/openliberty9",
     returnStdout: true
  )
} 

def inc_tag() {
  sh (
     script: "curl -s http://ms-counter.ms-counter.k8s1.labo.local/inc/openliberty9",
     returnStdout: true
  )
} 


pipeline {

  environment {
    registry = "tkr/web-apl-openliberty"
    dockerImage  = ""
    dockerImage2 = ""    
    KUBECONFIG = credentials('admin.kubeconfig-k8s1-liberty9')
    KUBEYAML = "deployment.yaml"
    TAG =  inc_tag()
    
  }

  agent any
  stages {
    stage('GitLabからソースコード取得') {
      steps {
        echo 'Notify GitLab'
        updateGitlabCommitStatus name: 'build', state: 'pending'
        updateGitlabCommitStatus name: 'build', state: 'success'
	echo "${TAG}"
      }
    }

    stage('コンテナイメージのビルド') {
      steps {
        script {
          dockerImage  = docker.build registry + ":${TAG}"
        }
      }
    }

    stage('コンテナイメージの脆弱性検査') {
      steps {
        script {
          sh 'echo "check 1"'
        }
      }
    }

    stage('コンテナの単体テスト') {
      steps {
        script {
          sh 'echo "check 2"'
        }
      }
    }

    stage('コンテナレジストリへプッシュ') {
      steps {
        script {
          docker.withRegistry("https://harbor.labo.local","harbor") {
            dockerImage.push()
          }
        }
      }
    }

    stage('K8sクラスタへのデプロイ') {
      steps {
        script {
          sh 'kubectl cluster-info --kubeconfig $KUBECONFIG'
	  sh 'sed s/__BUILDNUMBER__/$TAG/ k8s-yaml/deploy.yaml > $KUBEYAML'
          sh 'cat -n $KUBEYAML'
          sh 'kubectl apply -f $KUBEYAML --kubeconfig $KUBECONFIG'
	  sh 'env'
        }
      }
    }
  }
}