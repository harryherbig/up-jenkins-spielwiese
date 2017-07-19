pipeline {
    options { buildDiscarder(logRotator(numToKeepStr: '20')) }
    agent {
        label 'medium'
    }
    stages {
        stage("checkout") {
            agent none
            steps {
                script {
                    checkout scm                   
                }
            }
        }

        stage("detect changes") {
            agent none
            steps {
                script {
                    List<String> funkotronModuleWhitelist = ['article', 'applications']
                    def allChanges = []
                    currentBuild.changeSets.each { cs -> // currentBuild.rawBuild.getChangeSets()
                        cs.getItems().each { item ->
                            item.getAffectedFiles().each { f ->
                                allChanges << f.path.split('/')[0]
                            }
                        }
                    }
                    def uniqueChangedPaths = allChanges.unique()
                    echo "detected following changes: $uniqueChangedPaths"
                    def validModules = uniqueChangedPaths.findAll { funkotronModuleWhitelist.contains(it) }
                    echo "Whitelisted modules with changes are: $validModules"
                }
            }
        }
    }
}
