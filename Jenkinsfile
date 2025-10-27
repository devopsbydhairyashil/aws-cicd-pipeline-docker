// Scripted Jenkins Pipeline (advanced & customizable)
node {
  stage('Checkout') {
    checkout([$class: 'GitSCM', branches: [[name: '*/main']],
              userRemoteConfigs: [[url: 'https://github.com/devopsbydhairyashil/ci-cd-pipeline-aws.git']]])
  }

  stage('Build Docker Image') {
    // Build docker image - assumes docker is available on Jenkins agent
    sh 'docker --version || true'
    def imageName = "dhairyashil/flask-app"
    sh "docker build -t ${imageName}:$BUILD_NUMBER ./app"
  }

  stage('Unit Test (container)') {
    // Optional: quick smoke test of built image
    sh "docker run --rm -d --name smoke_test -p 5001:5000 dhairyashil/flask-app:$BUILD_NUMBER || true"
    // (In real pipeline add proper container test suite; this is a placeholder)
    sh "sleep 3 || true"
    sh "docker ps -a || true"
    sh "docker rm -f smoke_test || true"
  }

  stage('Push to DockerHub') {
    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
      sh "echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin"
      sh "docker tag dhairyashil/flask-app:$BUILD_NUMBER dhairyashil/flask-app:latest"
      sh "docker push dhairyashil/flask-app:$BUILD_NUMBER || true"
      sh "docker push dhairyashil/flask-app:latest || true"
    }
  }

  stage('Prepare kubeconfig') {
    // Option A: Use Jenkins credential (kubeconfig) - recommended to store kubeconfig in a secret
    // This step expects a Jenkins secret text credential id 'kubeconfig'
    sh 'echo "Writing kubeconfig from credentials..."'
    withCredentials([string(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_CONTENT')]) {
      writeFile file: 'kubeconfig', text: env.KUBECONFIG_CONTENT
      sh 'export KUBECONFIG=$WORKSPACE/kubeconfig; kubectl version --client'
    }
  }

  stage('Deploy to EKS') {
    sh 'export KUBECONFIG=$WORKSPACE/kubeconfig'
    // Replace image tags in manifest dynamically
    sh "sed -i 's|dhairyashil/flask-app:latest|dhairyashil/flask-app:$BUILD_NUMBER|g' kubernetes/deployment.yaml || true"
    sh "kubectl apply -f kubernetes/deployment.yaml"
    sh "kubectl apply -f kubernetes/service.yaml"
  }

  stage('Cleanup') {
    // optional: remove local images to save space on agent
    sh "docker image prune -af || true"
  }

  stage('Notify') {
    // placeholder for notification (Slack / Email)
    echo 'Deployment finished. Add notifications as needed.'
  }
}
