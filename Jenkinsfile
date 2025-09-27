pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out Sample_Script branch from GitHub"
                git branch: 'Sample_Script', url: 'https://github.com/Arunkj99719/Performance-Testing.git'
            }
        }

        stage('Run JMeter') {
            steps {
                echo "Running JMeter script: Blazedemo_Script_4Dec.jmx"

                // Run JMeter inside the Jenkins workspace
                bat '''
                REM Delete old report folder if it exists
                if exist "%WORKSPACE%\\JMeterReport" rmdir /S /Q "%WORKSPACE%\\JMeterReport"

                REM Run JMeter non-GUI mode and generate new HTML report
                "E:\\Perf_Test\\Performance_Testing_KTDocument\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t "%WORKSPACE%\\Blazedemo_Script_4Dec.jmx" -l "%WORKSPACE%\\JMeterReport\\results.jtl" -e -o "%WORKSPACE%\\JMeterReport"
                '''
            }
        }

        stage('Publish Report') {
            steps {
                echo "Publishing JMeter HTML report"
                publishHTML(target: [
                    reportDir: 'JMeterReport',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Performance Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true
                ])
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Check the console output for details."
        }
    }
}
