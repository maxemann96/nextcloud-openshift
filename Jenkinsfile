pipeline {
    agent any
    stages {
        stage('Build info') {
            steps {
              sh 'env'
            }
        }
        stage('Checkout sources') {
            steps {
                checkout changelog: false, poll: false,
                    scm: [$class: 'GitSCM', branches: [[name: "${env.GIT_BRANCH}"]],
                    doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [],
                    userRemoteConfigs: [[url: "${env.GIT_URL}"]]]
            }
        }
        stage('Apply configuration update') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject(/*"${env.PROJECT_NAME}"*/) {
                            def template = readFile 'nextcloud.yaml'
                            def config = openshift.process(template,
                              '-p', "NEXTCLOUD_HOST=${env.NEXTCLOUD_HOST}")
                            openshift.apply(config)
                        }
                    }
                }
            }
        }
    }
}