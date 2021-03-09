pipeline {
	agent any
	options {
		buildDiscarder(logRotator(numToKeepStr: '5'))
		disableConcurrentBuilds()
	}
	environment {
		IMAGE        = 'alpine-nginx-full'
		BUILDX_NAME  = "${IMAGE}_${GIT_BRANCH}"
		// Software versions; OpenResty does not support Lua >= 5.2
		OPENRESTY_VERSION = '1.19.3.1'
		LUA_VERSION       = '5.1.5'
		LUAROCKS_VERSION  = '3.3.1'
	}
	stages {
		stage('Environment') {
			parallel {
				stage('Master') {
					when {
						branch 'master'
					}
					steps {
						script {
							env.BASE_TAG                = 'latest'
							env.BUILDX_PUSH_TAGS        = "-t docker.io/techizvn/${IMAGE}:${BASE_TAG}"
							env.BUILDX_PUSH_TAGS_NODE   = "-t docker.io/techizvn/${IMAGE}:node"
						}
					}
				}
			}
		}
		stage('Base Build') {
			environment {
				BUILDX_NAME = "${IMAGE}_${GIT_BRANCH}_base"
			}
			steps {
				sh "./scripts/buildx --push ${BUILDX_PUSH_TAGS}"
			}
		}
		stage('Other Builds') {
			parallel {
				stage('Node') {
					environment {
						BUILDX_NAME  = "${IMAGE}_${GIT_BRANCH}_node"
					}
					steps {
						sh 'sed -i "s/BASE_TAG/${BASE_TAG}/g" Dockerfile.node'
						sh "./scripts/buildx --push -f Dockerfile.node ${BUILDX_PUSH_TAGS_NODE}"
					}
				}
			}
		}
	}
}
