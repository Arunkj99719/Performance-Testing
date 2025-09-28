pipeline {
    agent any

    environment {
        // JMeter installation path (update if installed elsewhere)
        JMETER_BIN  = "E:\\Perf_Test\\Perf_JMeter\\apache-jmeter-5.6.3\\bin\\jmeter.bat"

        // Report and CSV inside Jenkins workspace
        REPORT_DIR  = "${WORKSPACE}\\JMeter_Report_${BUILD_NUMBER}"
        CSV_FILE    = "${WORKSPACE}\\jenkins_test_${BUILD_NUMBER}.csv"

        // Repository-relative JMX path
        JMX_PATH    = "${WORKSPACE}\\JMeterScripts\\Blazedemo_Script_4Dec.jmx"
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
                echo "Running JMeter script: ${JMX_PATH}"
                bat """
                REM === Create report folder in workspace ===
                mkdir "${REPORT_DIR}"

                REM === Run JMeter test ===
                "${JMETER_BIN}" -n -t "${JMX_PATH}" -l "${CSV_FILE}" -e -o "${REPORT_DIR}"
                """
            }
        }

        stage('Publish HTML Report') {
            steps {
                echo "Publishing JMeter HTML report"
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: "${REPORT_DIR}",
                    reportFiles: 'index.html',
                    reportName: "JMeter Performance Report (Build ${BUILD_NUMBER})"
                ])
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully! HTML report is available in Jenkins."
        }
        failure {
            echo 'Pipeline failed. Check console output for errors.'
        }
    }
}
