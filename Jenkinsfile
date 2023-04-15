podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: maven
        image: maven:3.8.1-jdk-8
        command:
        - sleep
        args:
        - 99d
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''') {
  node(POD_LABEL) {
    stage('Get Simple Hello World App') {
      checkout scm
      container('maven') {
        stage('Test Hello World App') {
          sh '''
          echo "This could be a test"
          '''
        }
      }
    }

    stage('Build Hello World App') {
      container('kaniko') {
        stage('Upload to DockerHub') {
          sh "/kaniko/executor --context `pwd` --destination michaelcade1/90daysofdevops:${env.BUILD_ID}"
          sh "/kaniko/executor --context `pwd` --destination michaelcade1/90daysofdevops:latest"  
        }
      }
    }

  }
}