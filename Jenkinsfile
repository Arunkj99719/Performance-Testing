pipeline {
    agent any

    // Parameterized pipeline
    parameters {
        string(name: 'JMETER_SCRIPT', defaultValue: 'Blazedemo_Script_4Dec.jmx', description: 'Enter the JMeter script to run from the repo')
    }

    environment {
        // Update JMETER_HOME to your local JMeter path
        JMETER_HOME = 'E:\\Perf_Test\\Performance_Testing_KTDocument\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out Sample_Script branch from GitHub"
                git branch: 'Sample_Script', url: 'https://github.com/Arunkj99719/Performance-Testing.git'
            }
        }

        stage('Run JMeter') {
            steps {
                echo "Running JMeter script: ${params.JMETER_SCRIPT}"

                bat """
                REM Delete old report folder if it exists
                if exist report rmdir /S /Q report

                REM Run JMeter in non-GUI mode
                "${JMETER_HOME}\\bin\\jmeter.bat" -n -t "%WORKSPACE%\\${params.JMETER_SCRIPT}" -l "%WORKSPACE%\\results.jtl" -e -o "%WORKSPACE%\\report"
                """
            }
        }

        stage('Publish Report') {
            steps {
                echo "Publishing JMeter HTML report"
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

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check console output for errors."
        }
    }
}
