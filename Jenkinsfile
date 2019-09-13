#!groovy
// Copyright 2019 mbr targeting GmbH. All Rights Reserved.

pipeline {

  agent any

  options {
    skipStagesAfterUnstable()
  }

  environment {
    /*
     * Fix for docker "Read timed out"
     * https://github.com/docker/compose/issues/3927
     */
    DOCKER_CLIENT_TIMEOUT=120
    COMPOSE_HTTP_TIMEOUT=120
    GIT_COMMIT_HASH = sh (script: "git log -1 --format=%h", returnStdout: true)
    COMPOSE_PROJECT_NAME = "$env.BUILD_TAG"
    SERVICE_NAME = "java-spring-template"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout([
                $class           : 'GitSCM',
                branches         : scm.branches,
                extensions       : scm.extensions + [[$class: 'CleanCheckout']],
                userRemoteConfigs: scm.userRemoteConfigs,
        ])
      }
    }

    stage('Build Images') {
      steps {
        sh "docker build -t ${SERVICE_NAME} ."
        sh "docker tag ${SERVICE_NAME} docker.m6r.eu/${SERVICE_NAME}:${GIT_COMMIT_HASH}"
        sh "docker push docker.m6r.eu/${SERVICE_NAME}:${GIT_COMMIT_HASH}"
      }
    }
  }
}
