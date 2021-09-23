boolean isTriggeredByTimer = "${currentBuild.getBuildCauses('hudson.triggers.TimerTrigger$TimerTriggerCause')}" != '[]'
boolean isTriggeredByUser = "${currentBuild.getBuildCauses('hudson.model.Cause$UserIdCause')}" != '[]'
String cronString = BRANCH_NAME == 'main' ? 'H 22 * * *' : ''
echo "isTriggeredByTimer = ${isTriggeredByTimer}"
echo "isTriggeredByUser = ${isTriggeredByUser}"

node {

    properties([
        pipelineTriggers([cron(cronString)]),
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '14', numToKeepStr: ''))
    ])

    stage('Build All Jobs') {
        if (isTriggeredByTimer || isTriggeredByUser) {

            def hierarchy = [
                ['Build-LicenseFeature'],
                ['Library-EMFEditUtils'],
            ]

            hierarchy.each { repoNames ->
                def buildJobs = [:]
                repoNames.each { repoName ->
                    def jobPrefix = "MDSD-Tools/${repoName}/"
                    ['main', 'master'].each {
                        def jobName = "$jobPrefix${it}"
                        if (Jenkins.instance.getItemByFullName(jobName)) {
                            buildJobs["${repoName}"] = {
                                build job: jobName, propagate: false
                            }
                        }
                    }
   

                }
                parallel buildJobs
            }

        } else {
            echo "Build not triggered by user or timer. Doing nothing."
        }
    }

}
