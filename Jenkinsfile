pipeline{
    agent{
        label "jenkins-worker-1"
    }
    tools{
        maven 'MAVEN_HOME'
    }
    stages{
        stage("Pull the Code From SCM"){
            steps{
                echo "========Pull the Code From SCM========"
                git branch: 'master',
                    url: 'https://github.com/Narsi-Myteaching/weshopify-api-gateway.git'
                echo "========source code pulling completed========"
            }
        }
        stage("Build the Source Code"){
            steps{
                echo "========Code Building is Starting========"
                sh 'mvn clean package -DskipTests=true'
                echo "========Artifact Generated========"
            }
        }
        stage("Sonar Quality Analysis"){
            steps{
                echo "========Sonar Quality Gate Starting========"
                sh 'mvn verify sonar:sonar -Dsonar.projectKey=weshopify-platform-api-gateway -Dsonar.host.url=http://65.0.152.251:9000/ -Dsonar.login= sqp_b8a39c1c9f0077934d984d520285bf4111370023 -DskipTests=true'
                echo "========Sonar Quality Gate Analyzed the Artifact========"
            }
        }
        stage("Deploy to Artifactory"){
            steps{
                echo "========Deploying to Artifactory Started========"
                sh 'mvn deploy'
                echo "========Artifact Deploy is Completed========"
            }
        }
        stage("copy the files to ansible server"){
            steps{
                echo "Connecting to Ansible Server"
                sshagent(['ANSIBLE_SERVER']){
                    sh 'scp Dockerfile ansible-admin@192.168.0.4:/opt/weshopify-api-gateway/ci-files'
                    sh 'scp weshopify-api-gateway-playbook.yml ansible-admin@192.168.0.4:/opt/weshopify-api-gateway/ci-files'
                    sh 'scp jfrog.sh ansible-admin@192.168.0.4:/opt/weshopify-api-gateway/ci-files'
                    sh '''
                        ssh -tt ansible-admin@192.168.0.4 << EOF
                            ansible-playbook /opt/weshopify-api-gateway/ci-files/weshopify-api-gateway-playbook.yml
                            exit
                        EOF
                    '''
                }
            }
        }
    }
}
