pipeline {
  agent {

    docker {
      image 'kmi8652/build:1.1'
      args '-v /var/run/docker.sock:/var/run/docker.sock -u root:root'
    }

  }

  stages {

    stage('Copy source with configs') {
      steps {
        sh 'rm -rf *'
        sh 'git clone https://github.com/boxfuse/boxfuse-sample-java-war-hello.git && git clone https://github.com/snuffles765/lesson11.git'
        }
    }

    stage('Build war') {
      steps {
        sh 'cd boxfuse-sample-java-war-hello && mvn package'
      }
    }

    stage('Make docker image') {
      steps {
        sh 'cp boxfuse-sample-java-war-hello/target/hello-*.war lesson11/Dockerfiles/stage && cd lesson11/Dockerfiles/stage && docker build -t myboxfuse:$tag .'
        sh '''docker tag myboxfuse:$tag kmi8652/myboxfuse:$tag && docker push kmi8652/myboxfuse:$tag'''

      }
    }

    stage('Run docker on stage') {
      steps {
        sh 'ssh-keyscan -H 62.84.125.102 >> ~/.ssh/known_hosts'
        sh ''' ssh jenkins@62.84.125.102 << EOF
	    docker rm -f $(docker ps -a -q --filter="name=myboxfuse") 2> /dev/null
	    docker rmi -f $(docker images -q --filter "reference=kmi8652/myboxfuse") 2> /dev/null
        docker run -d --name myboxfuse -p 8080:8080 kmi8652/myboxfuse:$tag
EOF'''
      }
    }
  }
 }