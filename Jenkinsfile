PER_COMMIT_BUILD_TYPE = "per-commit"
PULL_REQUEST_TYPE = "pull-request"
BUILD_NOW_TYPE = "build-now"

def build_type() {
	cause = "${currentBuild.getBuildCauses()[0]["_class"]}"
	switch(cause) {
		case 'hudson.triggers.SCMTrigger$SCMTriggerCause':
			build_type = PER_COMMIT_BUILD_TYPE
			break
		case 'hudson.model.Cause$UserIdCause':
			build_type = BUILD_NOW_TYPE
			break
		case 'hudson.plugins.git.GitStatus$CommitHookCause':
			build_type = PER_COMMIT_BUILD_TYPE
			break
		case 'hudson.plugins.tfs.TeamPushCause':
			build_type = PULL_REQUEST_TYPE
			break
	}
	return build_type
}

def get_branch(pull_request_url) {
	def regex = /^origin-pull(.*)/
	def check = env.GIT_BRANCH ==~ regex
	if (check) {
		withCredentials([
			string(credentialsId: 'azure_username', variable: 'username'),
			string(credentialsId: 'azure_token', variable: 'token')
		]) {
			source_branch = sh(label: 'Source Branch PR Info', script: "curl -u \"${username}\":\"${token}\" \"${pull_request_url}\" \
				| jq \'.value | .[] |  select(.lastMergeCommit.commitId==\"${env.GIT_COMMIT}\")| .sourceRefName \' \
				| cut -d/ -f3- | tr -d '\"' " , returnStdout: true).trim()
		}
		branch = sh(returnStdout: true, script: "echo ${source_branch}").trim().take(100)

	} else {
		branch = sh(returnStdout: true, script: "echo ${GIT_BRANCH} | sed 's@origin/@@' ").trim().take(100)
	}
	return branch
}


pipeline {

    options {
        timeout(time:20, unit: 'MINUTES')
    }

    agent {
        node {
            label '1604-slave'
        }
    }

    environment {
        project_name = 'hamgam'
        pull_request_url = "https://code.mahsan.co/fortest/testing/_apis/git/repositories/cb8ff1dc-7986-47ed-b1ca-5fbcc450896a/pullrequests?api-version=5.1"

    }

    stages {
        stage('Prepare') {
            steps {
                echo "Start Build Number #${currentBuild.number}"
                echo 'Installing requirements ...'
//                 sh 'sudo apt-get update'
                sh 'sudo apt-get --allow-unauthenticated install -y python3-pip jq curl'
		sh 'sudo apt-get install -y python3.7-dev python3.7-venv'
                script {
                    reports = readJSON text: '{}'
//                     catchError {sh "rm -rf ${env.JOB_NAME}*"}
//                     deleteDir()
                }
            }
        }

//         stage('Clone') {
// 			steps {
// 				echo "checkout ${project_name} ..."
// 				script {
//
// 					reports.clone = readJSON text: '{}'
// 					commitNumber = ""
// 					id = ""
// 					tags = ""
// 					branch = ""
// 					full_id = ""
//
// 					if (build_type ==~ BUILD_NOW_TYPE) {
// 						checkout([$class: 'GitSCM', branches: [[name: "*/develop"]], userRemoteConfigs: [[credentialsId: repo_credentials_id, url: 'https://code.mahsan.net/shetab/sarand/_git/sarand']]])
// 						catchError {
// 							branch = "develop"
// 						}
// 					} else {
// 						checkout scm
// 						catchError {
// 							branch = get_branch(pull_request_url)
// 							if (branch ==~ "develop") {
// 								build_type = PER_COMMIT_BUILD_TYPE
// 							}
// 						}
// 					}
//
// 					catchError {
// 						commitNumber = sh(returnStdout: true, script: 'git rev-list --count HEAD').trim()
// 						id = sh(returnStdout: true, script: 'git describe  --abbrev=12 --always').trim()
// 						tags = sh(returnStdout: true, script: 'git tag').trim().take(100)
// 						full_id = sh(returnStdout: true, script: 'git describe  --abbrev=40 --always').trim()
// 						normalized_branch_name = sh(returnStdout: true, script: "echo ${branch} | tr '/' '_' ").trim().take(100)
// 					}
// 					catchError {
// 						issue = sh(returnStdout: true, script: 'issue-number').trim()
// 					}
// 					reports.clone.commit_number = "${commitNumber}" as String
// 					reports.clone.id = "${id}" as String
// 					reports.clone.tags = "${tags}" as String
// 					reports.clone.branch = "${branch}" as String
// 					reports.clone.full_id = "${full_id}" as String
// 				}
// 			}
// 		}

        stage('Setup') {
            steps {
                script {
                    echo 'Setuping ....'
		    echo 'Finished!!!'
                }
            }
        }

    }
}
