properties([[$class: 'HudsonNotificationProperty', endpoints: [[urlInfo: [urlOrId: 'http://hubot-issc29.herokuapp.com/hubot/jenkins-notify?room=general', urlType: 'PUBLIC']]]], pipelineTriggers([])])


node() {

	stage 'build'
		env.PATH="${tool 'M3'}/bin:${env.PATH}"
		checkout scm
		sh 'mvn clean package'
		archive 'target/*.war'

	stage 'integration-test'
		sh 'mvn verify'
		step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml', healthScaleFactor: 1.0])
}

if (env.BRANCH_NAME.length() < 3 || env.BRANCH_NAME.substring(0,3) != "PR-")
{

stage 'quality-and-functional-test'

	parallel(qualityTest: {
    	node() {
    		echo 'sonar scan'
        	// sh 'mvn sonar:sonar'
    	}
    }, functionalTest: {
    	echo 'selenium test'
        // build 'sauce-labs-test'
    })

    try {
        checkpoint('Testing Complete')
    } catch (NoSuchMethodError _) {
        echo 'Checkpoint feature available in Jenkins Enterprise by CloudBees.'
    }


stage 'approval'
	input 'Do you approve deployment to production?'

stage 'production'
	echo 'mvn cargo:deploy'

}
