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
                bat '"E:\\Perf_Test\\Performance_Testing_KTDocument\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\jmeter.bat" -n -t "E:\\Perf_Test\\Performance_Testing_KTDocument\\Performance_Testing_KTDocument\\Perf_training\\apache-jmeter-5.6.3\\bin\\Blazedemo_Script_4Dec.jmx" -l "E:\\Perf_Test\\RAW_FILES\\OUTPUT\\jenkins_test.csv" -e -o "E:\\Perf_Test\\RAW_FILES\\OUTPUT\\Jenkins_test_report"'
            }
        }

        stage('Publish Report') {
            steps {
                echo "Publishing JMeter HTML report"
                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'E:\\Perf_Test\\RAW_FILES\\OUTPUT\\Jenkins_test_report',
                    reportFiles: 'index.html',
                    reportName: 'JMeter Performance Report'
                ])
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully! HTML report published.'
        }
        failure {
            echo 'Pipeline failed. Check console output for errors.'
        }
    }
}
