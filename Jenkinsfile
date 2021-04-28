/*
 * Licensed to the Apache Software Foundation (ASF) under one or more
 * contributor license agreements.  See the NOTICE file distributed with
 * this work for additional information regarding copyright ownership.
 * The ASF licenses this file to You under the Apache License, Version 2.0
 * (the "License"); you may not use this file except in compliance with
 * the License.  You may obtain a copy of the License at
 *
 *     http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

/* groovylint-disable CompileStatic, NestedBlockDepth */

pipeline {
    agent any
    environment {
        registry = 'fireantbot/fireant'
        registryCredential = 'FIREANT_DOCKERHUB_ID'
        dockerImage = ''
    }
    triggers {
        cron('0 0 * * *')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout(
                    [$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/fireant-bot/fireant.git']]
                    ]
                )
            }
        }
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Upload') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
     /*
        stage('Stop') {
            steps {
                sh 'docker ps -f name=fireant -q | xargs --no-run-if-empty docker container stop'
                sh 'docker container ls -a -fname=fireant -q | xargs -r docker container rm'
            }
        }
        stage('Run') {
            steps {
                script {
                    dockerImage.run("""-p 8096:5000 --rm --name fireant --build-arg GITHUB_USERNAME=${GITHUB_USERNAME}
                                    |--build-arg GITHUB_PASSWORD=${GITHUB_PASSWORD}
                                    |--build-arg GITHUB_EMAIL=${GITHUB_EMAIL}
                                    |--build-arg REQUIRES_IO_TOKEN=${REQUIRES_IO_TOKEN}""".stripMargin())
                }
            }
        }*/
        stage('Slack') {
            steps {
                slackSend baseUrl: 'https://hooks.slack.com/services/',
                channel: '#jenkins',
                color: 'good',
                message: 'Jenkins Pipline executed successfully!',
                teamDomain: 'apachenutch401',
                tokenCredentialId: 'FIREANT_SLACK_TOKEN'
            }
        }
    }
}
