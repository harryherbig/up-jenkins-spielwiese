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

                    // maybe use this in the future?

//                    echo "<changes>"
//                    currentBuild.rawBuild.getChangeSets().each { cs ->
//                        cs.getItems().each { item ->
//                            item.getAffectedFiles().each { f ->
//                                echo f.path
//                            }
//                        }
//                    }
//                    echo "</changes>"
                }
            }
        }

        stage("detect changes") {
            agent none
            steps {
                script {
                    List<String> funkotronModuleWhitelist = ['article', 'applications']
                    // Using file because of Sandbox RejectedAccessException: unclassified method java.lang.String
                    String outfile = "changed_folders"
                    sh "rm $outfile || true"

                    // calculate affected modules to be build
                    if (env.BRANCH_NAME == 'master') {
                        echo "BUILDING MASTER: ${env.BRANCH_NAME}"
                        // changes between current head and commit before that
                        sh "git diff --name-only HEAD..HEAD^ | cut -d'/' -f-1 | sort | uniq > $outfile"
                    } else {
                        echo "BUILDING PR / ORIGIN branch: ${env.BRANCH_NAME}"
                        // changes between current head and master head
                        sh "git diff --name-only origin/master..HEAD | cut -d'/' -f-1 | sort | uniq > $outfile"
                    }

                    // filter affected modules for whitelisted modules
                    changedPaths = readFile(outfile)
                    echo "changed paths: $changedPaths"
                    validModules = changedPaths.split("( |\\n|\\r)+").findAll { funkotronModuleWhitelist.contains(it) }
                    echo "Whitelisted modules are: $validModules"
                }
            }
        }
    }
}
