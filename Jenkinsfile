node {
  stage('1.clone code') {
    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'bitbucket',
      usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
      sh 'rm -rf helloworld'
      sh "git clone http://'${USERNAME}':'${PASSWORD}'@139.199.89.250:7990/scm/cicd/helloworld.git"     
    }
  }
  stage('2.app test and build') {
    sh 'sleep 5'
        sh "touch stage02"
   }
  stage('3.local deploy') {
    sh 'sleep 5'
        sh "touch stage03"
   }
  stage('4.image build') {
    sh 'sleep 5'
        sh "touch stage04"
  }
  stage('5.image push to harbor') {
    sh 'sleep 5'
    dir('./helloworld/src') {
      withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'harbor',
        usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
          //sh 'docker login -u ${USERNAME} -p ${PASSWORD} ccr.ccs.tencentyun.com'    
          //sh 'docker build -t ccr.ccs.tencentyun.com/sf-mesh/examples-helloworld-v1:latest .'
          //sh 'docker push ccr.ccs.tencentyun.com/sf-mesh/examples-helloworld-v1:latest'
          sh 'docker login -u ${USERNAME} -p ${PASSWORD} 134.175.243.237'    
          sh 'docker build -t 134.175.243.237/library/examples-helloworld-v2:latest .'
          sh 'docker push 134.175.243.237/library/examples-helloworld-v2:latest'
      }       
    }
  }
  stage('6.deploy to istio') {
    sh 'sleep 5'
        dir('./helloworld/helm') {
          sh 'helm repo list'
          sh 'helm version'
          sh 'helm del --purge helloworld || echo "del complete!"'
          sh 'helm install . --name helloworld --namespace devops'
    }
  }
  stage('7.send email') {
    emailext body: '''Project: $PROJECT_NAME 
    Build Number: # $BUILD_NUMBER
    Build Status: $BUILD_STATUS
    Check console output at $BUILD_URL to view the results.

    http://134.175.243.237:8080/job/helloworld-gray-stage1/build?token=wanghongen

    http://134.175.243.237:8080/job/helloworld-gray-stage2/build?token=wanghongen

    ''', recipientProviders: [[$class: 'DevelopersRecipientProvider']], subject: '$BUILD_STATUS - $PROJECT_NAME - Build # $BUILD_NUMBER', to: 'wanghongenx@qq.com'
  }
}
