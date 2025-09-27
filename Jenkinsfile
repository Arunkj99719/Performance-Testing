pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'Sample_Script', url: 'https://github.com/Arunkj99719/Performance-Testing.git'
            }
        }

        stage('Run JMeter') {
            steps {
                bat 'jmeter -n -t Blazedemo_Script_4Dec.jmx -l results.jtl -e -o report'
            }
        }

        stage('Publish Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Performance Report'
                ])
            }
        }
    }
}
