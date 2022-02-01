pipeline {
    agent {label 'Ubuntu'}
    stages {

        stage('Dependency Resolution') {
            steps {
                echo "Dependency Resolution Started"
                sh(/npm install/)
                sh(/npm install sonar-scanner --save-dev/)
                sh(/npm install karma-sonarqube-unit-reporter --save-dev/)
                echo "Dependency Resolution End"
            }
        }

        stage('Compile & Build') {
            steps {
                echo "Code Compilation Started"
                sh(/ng build/)
                echo "Code Compilation End"
            }
        }
        
        stage('Test') {
            steps {
                echo "Test Started"
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh(/ng test --code-coverage --watch=false/)
                }
                echo "Test End"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "SonarQube Started"
                withSonarQubeEnv('localSonarQube') {
                    sh(/npm run sonar/)
                }
                echo "SonarQube End"
            }
        }

        stage('Artifact Publish') {
            steps {
                echo "Artifact Publish to Nexus Started"
                sh(/npm publish --registry http:\/\/www.mynexus.com:8081\/repository\/mynpmrepo\//)
                echo "Artifact Publish to Nexus End"
            }
        }
        
        stage('Build Image') {
            steps {
                echo "Build Image Started"
                sh(/docker build -f Dockerfile -t spring\/spring-node-angular-jwt:latest ./)
                echo "Build Image End"
            }
        }
        
        stage('Scan Image') {
            steps {
                echo "Scan Image Started"
                sh(/docker scan --file Dockerfile --json spring\/spring-node-angular-jwt:latest > spring-spring-node-angular-jwt_latest.json/)
                echo "Scan Image End"
            }
        }

        stage('Tag Image') {
            steps {
                echo "Tag Image Started"
                sh(/docker tag spring\/spring-node-angular-jwt:latest hub.docker.local:5000\/spring\/spring-node-angular-jwt:latest/)
                echo "Tag Image End"
            }
        }

        stage('Push Image') {
            steps {
                echo "Push Image Started"
                sh(/docker push hub.docker.local:5000\/spring\/spring-node-angular-jwt:latest/)
                echo "Push Image End"
            }
        }

    }
}
