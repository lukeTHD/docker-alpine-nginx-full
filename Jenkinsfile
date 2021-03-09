pipeline {
	agent any
	options {
		buildDiscarder(logRotator(numToKeepStr: '5'))
		disableConcurrentBuilds()
	}
	environment {
		IMAGE        = 'alpine-nginx-full'
		BUILDX_NAME  = "${IMAGE}_${GIT_BRANCH}"
		BASE_TAG  = "latest"
		// Software versions; OpenResty does not support Lua >= 5.2
		OPENRESTY_VERSION = '1.19.3.1'
		LUA_VERSION       = '5.1.5'
		LUAROCKS_VERSION  = '3.3.1'
	}
	stages {
		stage('Build') {
			steps {
				sh "./scripts/buildx --push -t docker.io/techizvn/${IMAGE}:latest"
				sh 'sed -i "s/BASE_TAG/${BASE_TAG}/g" Dockerfile.node'
				sh "./scripts/buildx --push -f Dockerfile.node -t docker.io/techizvn/${IMAGE}:node"
			}
		}
	}
}
