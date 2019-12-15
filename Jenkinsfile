pipeline {
	options {
		buildDiscarder(logRotator(numToKeepStr: '5'))
	}

	parameters {
		choice(name: 'Playbook', choices: ['Monitoring', 'ZAP', 'Pastel', 'poViewer', 'Purge'], description: 'The playbook you want to play.')
		choice(name: 'LogLevel', choices: ['Default', 'V', 'VV', 'VVV', 'VVVV'], description: 'The playbook log level.')
		string(name: 'poViewerVersion', defaultValue: 'X.X.X-SNAPSHOT', description: 'The poViewer version that matches the Docker image tag.')
	}

	agent {
		label 'docker'
	}

	environment {
		LOG_LEVEL = sh(returnStdout: true, script: 'echo $(([ ${LogLevel} = "Default" ] && echo "--") || ([ ${LogLevel} = "V" ] && echo "-v") || ([ ${LogLevel} = "VV" ] && echo "-vv") || ([ ${LogLevel} = "VVV" ] && echo "-vvv") || ([ ${LogLevel} = "VVVV" ] && echo "-vvvv")) | tr -d "\n"')
	}

	// Warning : the LOG_LEVEL environment variable must be in the last position in the ansible command because of the hack "--" to not set verbosity level

	stages {
		stage('Monitoring') {
			when {
				expression {
					params.Playbook == 'Monitoring'
				}
			}
			steps {
				sh """
					ansible-playbook -i inventory books/setup-monitoring.yml --limit master --tags influxdb,grafana,kapacitor --extra-vars "influxdbhost=10.238.114.188 kapacitorhost=10.238.114.188" ${LOG_LEVEL}
					ansible-playbook -i inventory books/setup-monitoring.yml --limit env --tags telegraf --extra-vars "influxdbhost=10.238.114.188 kapacitorhost=10.238.114.188" ${LOG_LEVEL}
				"""
			}
		}

		stage('ZAP') {
			when {
				expression {
					params.Playbook == 'ZAP'
				}
			}
			steps {
				sh """
					ansible-playbook -i inventory books/setup-zap.yml ${LOG_LEVEL}
				"""
			}
		}

		stage('Pastel') {
			when {
				expression {
					params.Playbook == 'Pastel'
				}
			}
			steps {
				sh """
					ansible-playbook -i inventory books/setup-pastel.yml ${LOG_LEVEL}
				"""
			}
		}

		stage('poViewer') {
			when {
				expression {
					params.Playbook == 'poViewer'
				}
			}
			steps {
				sh """
					ansible-playbook -i inventory books/setup-poviewer.yml --extra-vars "version=${poViewerVersion}" ${LOG_LEVEL}
				"""
			}
		}

		stage('Purge') {
			when {
				expression {
					params.Playbook == 'Purge'
				}
			}
			steps {
				sh """
					ansible-playbook -i inventory books/run-purge.yml ${LOG_LEVEL}
				"""
			}
		}
	}

	post {
		always {
			deleteDir()
		}
	}
}
