pipeline {
    agent {label "kernel && cross"}
    stages {
        stage("build") {
            steps {
                sh "make am335x_boneblack_defconfig"
                sh "ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make -j4"
                sh "tools/mkimage -C none -A arm -T script -d boot.cmd boot.scr"
                sh "tools/mkimage -C none -A arm -T script -d boot-emmc.cmd boot-emmc.scr"
                sh "mhamakedeb mahalia-bootloader.csv"
                stash name: "deb", includes: '*.deb'
                archiveArtifacts "*.deb"
            }
        }
        /*
        stage("debian packages for apt") {
            agent {label "aptly"}
            steps {
                // receive all deb packages from openmha build
                unstash "deb"
                archiveArtifacts "*.deb"

                // Copies the new debs to the stash of existing debs,
                sh "make storage"

                build job:         "/hoertech-aptly/master",
                      quietPeriod: 300,
                      wait:        false
            }
        }
        */
    }

    // Email notification on failed build taken from
    // https://jenkins.io/doc/pipeline/tour/post/
    // multiple recipients are comma-separated:
    // https://jenkins.io/doc/pipeline/steps/workflow-basic-steps/#-mail-%20mail
    post {
        failure {
            mail to: 't.herzke@hoertech.de',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
