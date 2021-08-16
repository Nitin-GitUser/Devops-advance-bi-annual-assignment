pipeline {
    agent any;
    
    stages{
        stage('Git checkout'){
            steps{
                git branch: 'feature', url: 'https://github.com/Nitin-GitUser/Devops-advance-bi-annual-assignment.git'
            }
        }
        stage('Build'){
            steps{
                sh '''
                    npm i
                    npm test
                '''
            }
        }
        stage('SonarQube analysis') {
            environment {
                scanner = tool 'SonarQubeScanner'
            }
            steps{
                withSonarQubeEnv('Test_Sonar') {
                     sh '''${scanner}/bin/sonar-scanner \
                             -Dsonar.projectKey=sonar-nitingoyal
                        '''
                }
            }
        }
        stage('Build Docker Image'){
            steps{
                sh '''
                    docker build . -t samplenodeapp:latest
                '''
            }
        }
        stage('Push Docker Image'){
            steps{
                sh '''
                    docker tag samplenodeapp:latest nitingoyal/samplenodeapp:feature
                    docker push nitingoyal/samplenodeapp:feature
                '''
            }
        }
        stage('Deployment'){
            parallel{
                stage('Docker Deployment'){
                    steps{
                         sh '''
                            running_container=`docker ps -a | grep samplenodeapp | awk '{print $1}'`
                            if [[ $running_container != '' ]];
                                then  
                                    docker stop $running_container;
                                docker rm $running_container 
                            fi
                            docker run -p 7400:7100 -d --name samplenodeapp samplenodeapp:latest
                        '''
                    }
                }
                stage('kubernetes Deployment'){
                    steps{
                         sh '''
                            kubectl apply -f deployment.yaml
                        '''
                    }
                }
            }
            
        }
    }
}