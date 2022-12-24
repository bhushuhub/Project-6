pipeline{
    agent{
        node{
            label 'master'
            customWorkspace '/mnt/project'
        }
    }
    stages{
        stage ('clone the repo'){
            steps{
                sh "rm -rf game_of_life*"
                sh "git clone 'https://github.com/bhushuhub/game_of_life.git'"
            }
        }
        stage ('maven build'){
            steps{
                dir ('/mnt/project/game_of_life'){
                    sh "mvn clean install"
                }
            }
        }
        stage ('provision of file permissions'){
            steps{
                sh "chmod -R 777 /mnt/project/game_of_life/target/gameoflife.war"
                sh "scp -i '/mnt/new-keypair.pem' /mnt/project/game_of_life/target/gameoflife.war ec2-user@172.31.81.62:/mnt/docker"
            }
        }
        stage ('create & run docker image'){
            agent{
                node{
                    label 'dev'
                    customWorkspace '/mnt/docker'
                }
            }
            steps{
                /*sh "sudo rm -rf gameoflife.war"*/
                sh "sudo docker build -t mytomcat:2.0 ."
                sleep 5
                sh "sudo docker run --name my-tomcat -itdp 8020:8080 mytomcat:2.0"
            }
        }
    }
}
