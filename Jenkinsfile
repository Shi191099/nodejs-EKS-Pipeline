environment {
        AWSCRED="aws-credentials"
        AWS_REGION="ca-central-1"
        ECR_REPO="805392809179.dkr.ecr.ca-central-1.amazonaws.com"
        ECR_REPO_NAME="clari5"
}
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
        - name: docker-config
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: docker-config
        configMap:
          name: docker-config
'''){
    
  node(POD_LABEL) {
    stage('Get a nodejs project') {
      git url: 'https://github.com/Shi191099/nodejs-EKS-Pipeline.git', branch: 'master'    
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
//                   withEnv(["AWS_ACCESS_KEY_ID=${env.AWS_ACCESS_KEY_ID}", "AWS_SECRET_ACCESS_KEY=${env.AWS_SECRET_ACCESS_KEY}", "AWS_DEFAULT_REGION=${env.AWS_DEFAULT_REGION}"]) {
                   
                     sh '''                       
                       /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=805392809179.dkr.ecr.ca-central-1.amazonaws.com/clari5:latest --destination=805392809179.dkr.ecr.ca-central-1.amazonaws.com/clari5:$BUILD_NUMBER
                     '''
//                  }
                withCredentials([[
                $class: 'AmazonWebServicesCredentialsBinding',
                accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                credentialsId: '',
                secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]){
                container('kaniko') {
                    sh '''
                    export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                    export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                    export AWS_DEFAULT_REGION=${AWS_REGION}
                    /kaniko/executor -f ${WORKSPACE}/kanikodemo/Dockerfile -c ${WORKSPACE}/kanikodemo --force --destination=${ECR_REPO}/${ECR_REPO_NAME}:$BUILD_NUMBER --destination=${ECR_REPO}/${ECR_REPO_NAME}:latest
                    '''
                    }
                }
              }
          }
      }
  }
}
