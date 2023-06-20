pipeline {
    agent any
    environment {
        repository = 'platformsi/spring-test' // Docker Hub ID와 repository 이름
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-platformsi') // Jenkins에 등록해 놓은 Docker Hub credentials 이름
        dockerImage = ''
        kubeconfig = '/home/config' // Kubernetes 구성 파일 경로
    }

    tools {
        maven 'maven_jenkins'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/94tiger/spring_test.git'
            }
        }

        stage('Maven 빌드') {
            steps {
                sh 'mvn -version'
                sh 'mvn clean install -DskipTests'
                // JAR 파일을 Docker 이미지 빌드 경로로 복사
                // sh 'cp target/demo-0.0.1-SNAPSHOT.jar /var/jenkins_home/workspace/Spring\\ Boot\\ Test/app.jar'
            }
        }

        stage('Docker Image 빌드') {
            steps {
                script {
                    // sh 'mv target/demo-0.0.1-SNAPSHOT.jar app.jar'
                    // sh 'mv app.jar app.jar'
                    dockerImage = docker.build repository + ":latest"
                }
            }
        }

        stage('Docker Hub 업로드') {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u platformsi --password-stdin' // docker hub 로그인
                    sh 'docker push $repository:latest' //docker push
                    // sh 'docker push $repository:$BUILD_NUMBER' //docker push
                }
            }
        }

        // stage('Run Docker Image') {
        //     steps {
        //         script {
        //             sh 'docker run -d -p 15000:8080 $repository:$BUILD_NUMBER'
        //         }
        //     }
        // }

        stage('Kubernetes 배포') {
            steps {
                script {
                    sh '''
                        export KUBECONFIG=$kubeconfig
                        kubectl --insecure-skip-tls-verify get deployments
                        kubectl --insecure-skip-tls-verify set image deployment/my-app-deployment my-app-container=platformsi/spring-test:latest
                    '''
                    // kubectl --insecure-skip-tls-verify set image deployment/my-app-container my-app-container=$repository:$BUILD_NUMBER
                    // export KUBECONFIG=$kubeconfig
                    // kubectl set image deployment/my-app-container my-app-container=$repository:$BUILD_NUMBER
                    // kubectl rollout status deployment/my-app-container
                }
            }
        }

        // stage('Docker Image 제거') {
        //     steps {
        //         sh "docker rmi $repository:$BUILD_NUMBER" // Docker 이미지 제거
        //     }
        // }
    }
}