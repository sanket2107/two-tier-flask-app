pipeline {
    agent { label "dev" }

    stages {

        stage("Code") {
            steps {
                git url: "https://github.com/sanket2107/two-tier-flask-app.git", branch: "master"
            }
        }

        stage("Build") {
            steps {
                
                sh "docker build --no-cache -t two-tier-flask-app ."
            }
        }

        stage("Test") {
            steps {
                echo "test completed"
            }
        }

        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "DockerHubCred",
                    usernameVariable: "dockerHubUser",
                    passwordVariable: "dockerHubPass"
                )]) {
                    sh """
                    echo "$dockerHubPass" | docker login -u "$dockerHubUser" --password-stdin
                    docker tag two-tier-flask-app $dockerHubUser/two-tier-flask-app:latest
                    docker push $dockerHubUser/two-tier-flask-app:latest
                    """
                }
            }
        }

        stage("Deploy") {
            steps {
                sh """
                docker compose down
                docker compose pull flask-app
                docker compose up -d flask-app
                """
            }
        }
    }

post {
    success{
        emailtext(
            subject: "Build was Successfull", 
            body: "Congratulations! Your build was successfull ",
            to: "sanketdeshmukh885@gmail.com"
        )
    failure{
        emailtext(
            subject: "Build was Failed !",
            body: "Bad News:Your build was failed ",
            to: "sanketdeshmukh885@gmail.com"
        )
    }
    }
}
}
