pipeline {
    agent any
    triggers {
        pollSCM('*/1 * * * *')  
    }
    stages {
        stage('building-docker-image') {
            steps {
                buildDockerImage()
            }
        }
        stage('deploy-to-dev') {
            steps {
                deploy("DEV")
            }
        }
        stage('test-on-dev') {
            steps {
                runApiTests("DEV")
            }
        }
        stage('deploy-to-stg') {
            steps {
                deploy("STG")
            }
        }
        stage('test-on-stg') {
            steps {
                runApiTests("STG")
            }
        }
        stage('deploy-to-prd') {
            steps {
                deploy("PRD")
            }
        }
        stage('test-on-prd') {
            steps {
                runApiTests("PRD")
            }
        }
    }
}

def buildDockerImage() {
    echo "Building Docker image.."
    sh "docker build -t sophie14/python-greetings-app:latest ."

    echo "Pushing image to Docker Hub.."
    sh "docker push sophie14/python-greetings-app:latest"
}

def deploy(String environment) {
    echo "Deployment triggered on ${environment} env..."
    String lowerCaseEnv = environment.toLowerCase()

    sh "docker pull sophie14/python-greetings-app:latest"
    sh "docker compose down greetings-app-${lowerCaseEnv}"
    sh "docker compose rm greetings-app-${lowerCaseEnv}"
    sh "docker compose up -d greetings-app-${lowerCaseEnv}"
}

def runApiTests(String environment) {
    echo "Running API tests on ${environment} env..."
    sh "docker pull sophie14/api-tests:latest"
    sh "docker run --network=host --rm sophie14/api-tests:latest run greetings greetings_${environment}"
}
