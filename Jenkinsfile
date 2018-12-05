/*
 * Copyright 2018 Dematic, Corp.
 * Licensed under the MIT Open Source License: https://opensource.org/licenses/MIT
 */

#!groovy

properties([
        buildDiscarder(logRotator(numToKeepStr: '5')),
        gitLabConnection('gitlab')
])


timestamps {
    if (env.BRANCH_NAME != 'master') {
        echo "Skipping build on feature branch"
        return
    }

    node {
        ansiColor('xterm') {
            stage('release') {
                checkout scm
                gitlabCommitStatus('release') {
                    def version = releaseVersion()
                    currentBuild.displayName = version
                    sh "git tag v" + version
                    sh 'git push --tags'
                }
            }
        }
    }
}

def releaseVersion() {
    def lines = readFile("CHANGELOG.md").split("\n")
    for (i = 0; i < lines.length; i++) {
        def m = lines[i] =~ /##\s+\[(\d+\.\d+\.\d+.*)]/
        if (m) {
            return m.group(1)
        }
    }
    error("No semver version defined in the CHANGELOG.md")
}
