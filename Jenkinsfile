pipeline {
    agent {
        kubernetes {
            yamlFile 'agent.yaml'
        }
    }

    environment {
        APP_VERSION = '3.0.0'
        IMAGE_REPO = 'carolaromero/lab3-carola-romero'
        IMAGE_TAG = 'carola-romero'
    }

    stages {
        stage('Install') {
            steps {
                container('node') {
                    sh '''
                        corepack enable
                        corepack prepare pnpm@9.15.4 --activate
                        pnpm install --frozen-lockfile
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                container('node') {
                    sh 'pnpm test -- --runInBand'
                }
            }
        }

        stage('Build') {
            steps {
                container('node') {
                    sh 'pnpm build'
                }
            }
        }

        stage('Push') {
            steps {
                container('docker') {
                    withCredentials([
                        usernamePassword(
                            credentialsId: 'dockerhub-carola',
                            usernameVariable: 'DOCKERHUB_USER',
                            passwordVariable: 'DOCKERHUB_TOKEN'
                        )
                    ]) {
                        sh '''
                            until docker info >/dev/null 2>&1; do
                                echo "Esperando que Docker DinD esté disponible..."
                                sleep 2
                            done

                            echo "$DOCKERHUB_TOKEN" | docker login \
                                -u "$DOCKERHUB_USER" \
                                --password-stdin

                            docker build \
                                -t "$IMAGE_REPO:$IMAGE_TAG" \
                                -t "$IMAGE_REPO:$APP_VERSION" .

                            docker push "$IMAGE_REPO:$IMAGE_TAG"
                            docker push "$IMAGE_REPO:$APP_VERSION"

                            docker logout
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                container('kubectl') {
                    sh '''
                        TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)

                        kubectl \
                          --server=https://kubernetes.default.svc \
                          --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt \
                          --token="$TOKEN" \
                          apply -f entrega.yaml

                        kubectl \
                          --server=https://kubernetes.default.svc \
                          --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt \
                          --token="$TOKEN" \
                          rollout restart deployment/app-carola-romero \
                          -n ns-carola-romero

                        kubectl \
                          --server=https://kubernetes.default.svc \
                          --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt \
                          --token="$TOKEN" \
                          rollout status deployment/app-carola-romero \
                          -n ns-carola-romero \
                          --timeout=120s
                    '''
                }
            }
        }
    }
}

