pipeline {
       agent {
    kubernetes {
      yaml """
kind: Pod
spec:
  containers:
  - name: sonar-scanner-cli
    image: sonarsource/sonar-scanner-cli:latest
    imagePullPolicy: Always
    command:
     - cat
    tty: true
  - name: maven
    image: gradle:jdk14
    imagePullPolicy: Always
    tty: true
    command:
     - cat

    
"""
    }
       }
    stages {
        stage('maven') {
            steps {
                script{
                     container('maven') {
                    sh '''
                          chmod +x gradlew
                        ./gradlew clean assembledev
                    '''
                     }
                }
            }
        }
        stage('build the app') {
            steps {
                script{
                    container('sonar-scanner-cli') {
                      sh "ls -l"
                      sh "touch ayman.txt"
                      sh "echo 'from hadolint to others' > ayman.txt"
                      echo "******"
                      sh "ls -l"
                      withSonarQubeEnv(credentialsId: 'asd') {
                              sh '''
                     sonar-scanner \
                    -Dsonar.projectKey="myandroid" \
                    -Dsonar.sources=. \
                    -Dsonar.branch.name=$BRANCH_NAME
                    '''
                    }
                    }
                }
            }
        }
  
    }
    post{
        always { 
            cleanWs()
        }
    }
}

def branchToConfig(branch) {
    envs=  ['development':
                ['goal':'dev',
                'app': 'Android-Client-Dev'],
            'stage':
                ['goal':'stage',
                'app': 'Android-Client-stage'],
            'master':
                ['goal':'prod',
                'app': 'Android-Client']
            ]
    return envs[branch]
}