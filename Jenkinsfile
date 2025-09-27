pipeline {
    agent any

    parameters {
        string(name: 'JMETER_SCRIPT', defaultValue: 'Blazedemo_Script_4Dec.jmx', description: 'Enter the JMeter script to run from the repo')
    }

    environment {
        JMETER_HOME = 'C:\\apache-jmeter-5.6' // Update to your JMeter installation path
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'Sample_Script', url: 'https://github.com/Arunkj99719/Performance-Testing.git'
            }
        }

        stage('Run JMeter') {
            steps {
                bat """
                REM Delete old report folder if it exists
                if exist report rmdir /S /Q report

                REM Run JMeter in non-GUI mode and generate new HTML report
                ${JMETER_HOME}\\bin\\jmeter.bat -n -t ${WORKSPACE}\\${JMETER_SCRIPT} -l ${WORKSPACE}\\results.jtl -e -o ${WORKSPACE}\\report
                """
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

