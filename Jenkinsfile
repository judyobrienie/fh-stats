#!groovy

// https://github.com/feedhenry/fh-pipeline-library
@Library('fh-pipeline-library') _

stage('Trust') {
    enforceTrustedApproval()
}

fhBuildNode([labels: ['nodejs6']]) {

    dir('fh-statsd') {

        final String COMPONENT = 'fh-stats'
        final String VERSION = getBaseVersionFromPackageJson()
        final String BUILD = env.BUILD_NUMBER
        final String DOCKER_HUB_ORG = "feedhenry"
        final String CHANGE_URL = env.CHANGE_URL

        stage('Install Dependencies') {
            npmInstall {}
        }

        stage('Unit Tests') {
            sh 'grunt fh-unit'
        }

        stage('Build') {
            gruntBuild {
                name = COMPONENT
            }
        }

        stage('Platform Update') {
            final Map updateParams = [
                    componentName: COMPONENT,
                    componentVersion: VERSION,
                    componentBuild: BUILD,
                    changeUrl: CHANGE_URL
            ]
            updateParams.componentName = 'fh-statsd'
            fhOpenshiftTemplatesComponentUpdate(updateParams)
        }

        stage('Build Image') {
            dockerBuildNodeComponent('fh-statsd', DOCKER_HUB_ORG)
        }
    }

}
