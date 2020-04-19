pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Deploy_to_staging'){
            when{
                branch 'master'
            }
            withCredentials([usernamePassword(credentialsId: 'Github_mine', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]{
                sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                    publishers[
                        sshPublisherDesc(
                            configName: 'staging',
                            sshCredentials:[
                                username: "$USERNAME",
                                encryptedPassphrase: "$USERPASS",
                            ],
                            transfers: [
                                sshTranfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                )
                            ]
                        )
                    ]
                )
            }
        }
        stage('Deploy_to_prod'){
            when{
                branch 'master'
            }
            input: [
                message: 'Is correct in staging'
            ]
            milestone(1)
            withCredentials([usernamePassword(credentialsId: 'Github_mine', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]{
                sshPublisher(
                    failOnError: true,
                    continueOnError: false,
                    publishers[
                        sshPublisherDesc(
                            configName: 'staging',
                            sshCredentials:[
                                username: "$USERNAME",
                                encryptedPassphrase: "$USERPASS",
                            ],
                            transfers: [
                                sshTranfer(
                                    sourceFiles: 'dist/trainSchedule.zip',
                                    removePrefix: 'dist/',
                                    remoteDirectory: '/tmp',
                                    execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                )
                            ]
                        )
                    ]
                )
            }
        }
    }
}
