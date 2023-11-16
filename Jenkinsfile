pipeline {
    agent any
     environment {
        YOUR_NAME = credentials("YOUR_NAME")
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t longehdocker/task2-db db
                docker build -t longehdocker/task2jenk flask-app
                docker build -t longehdocker/task2-nginx nginx
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push longehdocker/task2-db
                docker push longehdocker/task2jenk
                docker push longehdocker/task2-nginx
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                ssh jenkins@long-jenkins-deploy <<EOF
                export YOUR_NAME=${YOUR_NAME}
                docker network rm task2-net && echo "removed network" || echo "network already removed"
                docker network create task2-net
                docker stop mysql && echo "Stopped DB" || echo "DB is not running"
                docker rm mysql && echo "Removed DB" || echo "DB does not exist"                
                docker stop nginx && echo "Stopped nginx" || echo "nginx is not running"
                docker rm nginx && echo "removed nginx" || echo "nginx does not exist"
                docker stop flask-app && echo "Stopped flask-app" || echo "flask-app is not running"
                docker rm flask-app && echo "removed flask-app" || echo "flask-app does not exist"
                docker run -d --name mysql -e YOUR_NAME=${YOUR_NAME} longehdocker/task2-db
                docker run -d --name flask-app --network task2-net -e YOUR_NAME=${YOUR_NAME} longehdocker/task2jenk
                docker run -d --name nginx --network task2-net -p 80:80 longehdocker/task2-nginx
                '''
            }

        }

    }

}