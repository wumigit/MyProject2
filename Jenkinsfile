pipeline {
    agent any

    stages {
        stage('Git clone') {
            steps {
                git 'https://github.com/wumigit/MyProject2.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('mysonar')  {
                    sh 'mvn -f SampleWebApp/pom.xml clean package sonar:sonar'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true, credentialsId: 'Secret text'
            }
        }
        stage('Test') {
            steps {
                sh 'cd SampleWebApp && mvn test'
            }
        }
        stage('Build with maven') {
            steps {
                sh 'cd SampleWebApp && mvn package'
            }
        }
        stage('Deploy to Jfrog') {
            steps {
                rtUpload (
                    serverId: 'my-jfrog',
                    spec: '''{
                        "files": [
                            {
                                "pattern": "**/*.war",
                                "target": "my-repo/"
                            }
                        ]
                    }''', 
                )   
            }
        }

        stage('Deploy to tomcat') {
            steps {
               deploy adapters: [tomcat9(credentialsId: 'devops', 
               path: '', url: 'http://35.171.20.86:8080')], 
               contextPath: 'default', war: '**/*.war'
            }
        }
    }
}
