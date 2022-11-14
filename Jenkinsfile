podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: nodejs
        image: node:6-alpine
        command:
        - sleep
        args:
        - 999999
      - name: kubectl
        image: bitnami/kubectl
        command:
        - sleep
        args:
        - 9999999
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
    stage('Get a nodejs project') {
      git url: 'https://github.com/PottaAkhil/nodejs-OBT.git', branch: 'master'    
      container('nodejs') {
        stage('Build a nodejs project') {
          sh '''
            echo pwd
          '''
        }
      }
    }
    
    stage('Build nodejs Image') {
      container('kaniko') {
        stage('Build a Go project') {
          sh '''
            /kaniko/executor --context `pwd` --destination 805392809179.dkr.ecr.ap-south-1.amazonaws.com/nodejs-app:$BUILD_NUMBER && \
             /kaniko/executor --context `pwd` --destination 805392809179.dkr.ecr.ap-south-1.amazonaws.com/nodejs-app:latest
          '''
        }
      }
    }
   stage('Deploy'){
     container('kaniko') {       
                 sh 'kubectl apply -f flux.yaml'
                 
            } 
   }
}
}
