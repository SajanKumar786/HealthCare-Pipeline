pipeline {
    agent any

    environment {
        NAME = "medicure"
        VERSION = "${env.BUILD_ID}"
        GIT_COMMIT = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
        IMAGE_REPO = "sajankumar786"
        GIT_REPO_NAME = "Healthcare-App-GitOps-Pipeline"
        GIT_USER_NAME = "sajankumar786"
    }

    tools { 
        maven 'maven-3.8.6' 
    }
    
    stages {
        stage('Checkout git') {
            steps {
                git branch: 'main', url: "https://github.com/sajankumar786/${GIT_REPO_NAME}.git"
            }
        }
        
        stage('Build & JUnit Test') {
            steps {
                sh 'mvn clean install' 
            }
        }

        stage('SonarQube Analysis'){
            steps{
                withSonarQubeEnv('SonarQube-server') {
                    sh '''mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=healthcare-gitops-pipeline \
                    -Dsonar.projectName='healthcare-gitops-pipeline' \
                    -Dsonar.host.url=$sonarurl \
                    -Dsonar.login=$sonarlogin'''
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage('Docker Build') {
            steps {
                sh 'docker build -t ${IMAGE_REPO}/${NAME}:${VERSION}-${GIT_COMMIT} .'
            }
        }

        stage('Docker Image Scan') {
            steps {
                sh 'trivy image --format template --template "@/usr/local/share/trivy/templates/html.tpl" -o report.html ${IMAGE_REPO}/${NAME}:${VERSION}-${GIT_COMMIT}'
            }
        }    
        
        stage('Upload Scan report to AWS S3') {
            steps {
                sh 'aws s3 cp report.html s3://sajankumar786-devops-bucket/'
            }
        }
        
        stage('Docker Image Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', passwordVariable: 'password', usernameVariable: 'username')]) {
                    sh "echo ${password} | docker login -u ${username} --password-stdin"
                    sh 'docker push ${IMAGE_REPO}/${NAME}:${VERSION}-${GIT_COMMIT}'
                    sh 'docker rmi ${IMAGE_REPO}/${NAME}:${VERSION}-${GIT_COMMIT}'
                }
            }
        }
        
        stage('Update deployment Manifest') {
            steps {
                sh 'sed -i "s#sajankumar786/medicure:.*#${IMAGE_REPO}/${NAME}:${VERSION}-${GIT_COMMIT}#g" k8s/test-deployment.yaml'
                sh 'sed -i "s#sajankumar786/medicure:.*#${IMAGE_REPO}/${NAME}:${VERSION}-${GIT_COMMIT}#g" k8s/prod-deployment.yaml'
                sh 'cat k8s/test-deployment.yaml'
            }
        }
        
        stage('Commit & Push changes to Main') {
            steps {
                withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'GITHUB_TOKEN')]) {
                    sh "git config --global user.name 'sajankumar786'"
                    sh "git config --global user.email 'sajan12d37@gmail.com'"
                    sh "git remote set-url origin https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME}.git"
                    sh 'git add k8s/test-deployment.yaml k8s/prod-deployment.yaml'
                    sh "git commit -m 'Automated infrastructure image version bump: Build-${VERSION}-${GIT_COMMIT} [skip ci]'"
                    sh 'git push origin main'
                }    
            }
        }
    }

    post {
        always {
            sendSlackNotification()
        }
    }
}

def sendSlackNotification() {
    if (currentBuild.currentResult == "SUCCESS") {
        buildSummary = "Job_name: ${env.JOB_NAME}\n Build_id: ${env.BUILD_ID} \n Status: *SUCCESS*\n Build_url: ${BUILD_URL}\n"
        slackSend(channel: "#devops", token: 'slack-token', color: 'good', message: "${buildSummary}")
    } else {
        buildSummary = "Job_name: ${env.JOB_NAME}\n Build_id: ${env.BUILD_ID} \n Status: *FAILURE*\n Build_url: ${BUILD_URL}\n"
        slackSend(channel: "#devops", token: 'slack-token', color: "danger", message: "${buildSummary}")
    }
}