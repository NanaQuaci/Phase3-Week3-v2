pipeline {
    agent {
        label 'docker'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/NanaQuaci/Phase3-Week3-v2.git'
            }
        }

        stage('Run JMeter Test') {
            steps {
                // Clean old reports if any exists
                sh '''
                rm -rf "${WORKSPACE}/reports" "${WORKSPACE}/results.jtl"
                mkdir -p "${WORKSPACE}/reports"
                '''


                // Run JMeter in non-GUI mode
                sh '''
                jmeter -n -t ${WORKSPACE}/FakestoreAPI_Performance_Test_Plan.jmx \
                                       -l ${WORKSPACE}/results.jtl \
                                       -Jjmeter.save.saveservice.output_format=csv \
                                       -e -o ${WORKSPACE}/reports \
                                       -f
                '''
            }
        }

        stage('Verify JMeter Output') {
            steps {
                sh '''
                echo "Results file size:"
                ls -lh ${WORKSPACE}/results.jtl
                head -n 20 ${WORKSPACE}/results.jtl
                '''
            }
        }

        stage('Publish Report') {
            steps {
                publishHTML(target: [
                    allowMissing: false,
                    keepAll: true,
                    reportDir: 'reports',
                    reportFiles: 'index.html',
                    reportName: 'JMeter HTML Report'
                ])
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'results.jtl, reports/**', fingerprint: true

            script {
                            try {
                                //Determine status emoji and text
                                def statusEmoji = ""
                                def statusText = ""

                                if (currentBuild.result == 'SUCCESS') {
                                    statusEmoji = ":white_check_mark:"
                                    statusText = "success"
                                } else if (currentBuild.result == 'UNSTABLE') {
                                    statusEmoji = ":warning:"
                                    statusText = "unstable"
                                } else {
                                    statusEmoji = ":x:"
                                    statusText = "failed"
                                }

                                // Get commit hash (first 8 characters)
                                def commitHash = env.GIT_COMMIT ? env.GIT_COMMIT.take(8) : "unknown"

                                // Build the message
                                def message = "${statusEmoji} Fakestore API Tests finished with status: ${statusText}\n " +
                                             ":package: Repo: ${env.JOB_NAME}\n " +
                                             ":link: Commit: ${commitHash}\n " +
                                             ":bar_chart: Report: ${env.BUILD_URL}JMeter%20HTML%20Report/"
                                slackSend(
                                    channel: '#ci-alerts',
                                    color: currentBuild.result == 'SUCCESS' ? 'good' : 'danger',
                                    message: message
                                )
                            } catch (err) {
                                echo "Slack notification failed: ${err}"
                            }
                        }
                    }
        }
    }