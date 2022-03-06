def mvn
def server = Artifactory.server 'artifactory'
def rtMaven = Artifactory.newMavenBuild()
def buildinfo
def DockerTag(){
    def tag = sh script: 'git rev-parse head',returnStdout:true
    return tag
}
pipeline{
    agent{
        docker{
            image 'maven'
            args '-v $HOME/.m2:/root/.m2'
        }
    }
    options{
        timestamps()
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '10', numToKeepStr: '5')
    }

    environment {
        SCANNER_HOME = tool 'sonarserver'
        DOCKER_TAG = "DockerTag()"
      }
    
    stages{
        stage('Artifactory_Configuration'){
            steps{
                script{
                    rtMaven.tool = 'maven'
                    rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot', server: server
                    buildInfo = Artifactory.newBuildInfo()
                    rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot', server: server
                    buildInfo.env.capture = true
                }
            }
        }

        stage('Execute_Maven'){
            steps{
                script{
                    rtMaven.run pom: 'pom.xml', goals: 'clean install', buildInfo: buildInfo
                }
            }
        }
    }
}