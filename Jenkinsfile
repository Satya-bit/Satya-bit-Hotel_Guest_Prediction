pipeline{
    agent any

    environment{
        VENV_DIR = 'venv'
        GCP_PROJECT="analog-medium-456413-q6"
        GCLOUD_PATH="/var/jenkins_home/google-cloud-sdk/bin"

    }

    stages{
        stage('Cloning Github repo to Jenkins'){
            steps{
                script{
                    echo 'Cloning Github repo to Jenkins.......'
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-token', url: 'https://github.com/Satya-bit/Satya-bit-Hotel_Guest_Prediction.git']])
                }
            }
        }

        stage('Setting up our Virtual Environment and Installing Dependencies'){
            steps{
                script{
                    echo 'Setting up our Virtual Environment and Installing Dependencies.......'
                    sh '''
                    python -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate

                    pip install --upgrade pip
                    pip install -e .
                    '''

                }
            }
        }

         stage('Building and pushing image to GCR'){
            steps{
              withCredentials([file(credentialsId : 'gcp-key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                script{
                    echo 'Building and pushing image to GCR.......'
                    sh '''
                    export PATH=${PATH}:${GCLOUD_PATH}
                    gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                    GCLOUD CONFIG SET PROJECT ${GCP_PROJECT}
                    gcloud auth configure-docker --quiet

                    docker build -t gcr.io/${GCP_PROJECT}/hotel_guest_prediction .
                    docker push gcr.io/${GCP_PROJECT}/hotel_guest_prediction



                    '''
                    }
            }
        }
    }
}    