pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "deepak158/react-sample"
        DOCKER_CREDENTIALS_ID = "deepak-dockerhub"
        AWS_CREDENTIALS_ID = "aws-eks-credentials"
        AWS_REGION = "us-east-1"
        EKS_CLUSTER = "sample-EKS"
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    sh '''
                    rm -rf react || true
                    git clone https://github.com/deepakkishor15/Devops.git react
                    cd react
                    git fetch --all
                    git reset --hard origin/main
                    '''
                }
            }
        }

 stage('Install Dependencies & Fix Security Issues') {
    steps {
        script {
            sh '''
            export NVM_DIR="$HOME/.nvm"
            [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"

            NODE_VERSION="20" # Set your required Node.js version
            NODE_PATH=$(find "$NVM_DIR/versions/node" -name "v$NODE_VERSION*" | head -n 1)

            if [ -d "$NODE_PATH" ]; then
                export PATH="$NODE_PATH/bin:$PATH"
            else
                echo "Node.js v$NODE_VERSION not found!"
                exit 1
            fi

            node -v
            npm -v

            rm -rf node_modules package-lock.json
            npm install -g npm@latest
            npm install
            '''
        }
    }
}



        stage('Build React App') {
            steps {
                script {
                    sh '''
                    cd react
                    npm run build
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                    cd react
                    export DOCKER_BUILDKIT=1
                    docker build -t $DOCKER_IMAGE .
                    '''
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    withDockerRegistry([credentialsId: DOCKER_CREDENTIALS_ID, url: "https://index.docker.io/v1/"]) {
                        sh '''
                        docker push $DOCKER_IMAGE
                        '''
                    }
                }
            }
        }

        stage('Deploy to AWS EKS') {
            steps {
                script {
                    withCredentials([aws(credentialsId: AWS_CREDENTIALS_ID, region: AWS_REGION)]) {
                        sh '''
                        aws eks --region $AWS_REGION update-kubeconfig --name $EKS_CLUSTER
                        kubectl apply -f react/k8s/deployment.yaml
                        kubectl rollout status deployment/sample-EKS
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo "🚀 Deployment successful!"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}
