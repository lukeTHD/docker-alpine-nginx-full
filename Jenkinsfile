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
				sh "docker build --platform linux/amd64,linux/arm64,linux/arm/7 --progress plain -t docker.io/techizvn/${IMAGE}:latest --build-arg ${OPENRESTY_VERSION} --build-arg ${LUA_VERSION} --build-arg ${LUAROCKS_VERSION} ."
				sh 'sed -i "s/BASE_TAG/${BASE_TAG}/g" Dockerfile.node'
				sh "docker build --platform linux/amd64,linux/arm64,linux/arm/7 --progress plain -t docker.io/techizvn/${IMAGE}:node -f Dockerfile.node --build-arg ${OPENRESTY_VERSION} --build-arg ${LUA_VERSION} --build-arg ${LUAROCKS_VERSION} ."
				sh "docker push docker.io/techizvn/${IMAGE}:latest"
				sh "docker push docker.io/techizvn/${IMAGE}:node"
			}
		}
	}
}
