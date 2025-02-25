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
                    set -e
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
                    set -e
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"

                    NODE_VERSION="20" 
                    NODE_PATH=$(find "$NVM_DIR/versions/node" -name "v$NODE_VERSION*" | head -n 1)

                    if [ -d "$NODE_PATH" ]; then
                        export PATH="$NODE_PATH/bin:$PATH"
                    else
                        echo "Node.js v$NODE_VERSION not found!"
                        exit 1
                    fi

                    echo "Using Node.js version:"
                    node -v
                    npm -v

                    rm -rf node_modules package-lock.json
                    npm install -g npm@latest
                    npm install
                    '''
                }
            }
        }
    }
}
