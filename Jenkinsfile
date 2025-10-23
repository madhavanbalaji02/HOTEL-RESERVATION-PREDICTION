pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
        PROJECT = 'HOTEL-RESERVATION-PREDICTION'
        GCLOUD_PATH = '/var/jenkins_home/google-cloud-sdk/bin'
    }

    stages {
        stage('Clone GitHub Repo') {
            steps {
                script {
                    echo '✅ Cloning GitHub repository...'
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[
                            url: 'https://github.com/madhavanbalaji02/HOTEL-RESERVATION-PREDICTION.git',
                            credentialsId: 'github-token'
                        ]]
                    ])
                }
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    echo '🐍 Setting up Python environment...'
                    sh """
                        python3 -m venv ${VENV_DIR}
                        . ${VENV_DIR}/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt || true
                    """
                }
            }
        }

        stage('Train Model') {
            steps {
                script {
                    echo '🤖 Running model training pipeline...'
                    sh """
                        . ${VENV_DIR}/bin/activate
                        python3 src/model_training.py
                    """
                }
            }
        }

        stage('Evaluate Model') {
            steps {
                script {
                    echo '📊 Evaluating model performance...'
                    sh """
                        . ${VENV_DIR}/bin/activate
                        python3 src/model_evaluation.py
                    """
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                script {
                    echo '📦 Archiving artifacts...'
                    archiveArtifacts artifacts: 'artifacts/**/*.pkl', fingerprint: true
                }
            }
        }
    }

    post {
        success {
            echo '🎯 Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check console logs for details.'
        }
    }
}
