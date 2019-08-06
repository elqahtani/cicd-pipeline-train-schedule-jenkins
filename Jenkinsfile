pipeline {
	agent any 
		stages {
			stage ('Build') {
				steps {
					echo 'Running build automation'
					sh './gradlew build --no-daemon'
					archiveArtifacts artifacts: 'dist/trainSchedule.zip'
				}
		}

		stage('DeployToStaging'){
			when {
				branch "master"
			}
			steps {
				withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
					sshPublisher(
						failOnError: true, 
						continueOnError: false,
						publishers: [
							sshPublisherDesc(
								configName: 'staging',
								sshCredential: [
									username: "$USERNAME",
									encryptedPasphrase: "$USERPASS"
								],
								transfers: [
									sshTransfers (
										sourceFiles: 'dist/trainSchedule.zip',
										removeprefix: 'dist/',
										remotedirectory: '/tmp',
										execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm-rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
									)
								]
							)
						]
					)
				}
			}

		}
		

	}
}
