pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION    = 'us-east-2'
        STAGING_FUNCTION_NAME = 'sample-application-staging'
        STAGING_URL           = 'https://nphukndnle4gm37v3bffyhdvri0dvzaz.lambda-url.us-east-2.on.aws/'
        PRODUCTION_FUNCTION_NAME = 'sample-application-production'
        PRODUCTION_URL = 'https://x2kwlkaff4pjobsjjtgl4ssdce0ceiof.lambda-url.us-east-2.on.aws/'
    }

    stages {
        stage('Requirements') {
            steps {
                sh('''#!/bin/bash
                        python3 -m virtualenv local
                        . ./local/bin/activate
                        make requirements
                        ''')
            }
        }
        stage('Check') {
            parallel {
                stage('Check:Lint') {
                    steps {
                        sh('''#!/bin/bash
                                . ./local/bin/activate
                                make check lint
                                ''')
                    }
                }
                stage('Test') {
                    steps {
                        sh('''#!/bin/bash
                            . ./local/bin/activate
                            make test
                            ''')
                    }
                }
            }
        }
        stage('Build') {
            steps {
                sh('''#!/bin/bash
                        make build
                        ''')
            }
        }
        stage('Deploy Staging') {
            steps {
                sh('''#!/bin/bash
                        make deploy \
                            ENVIRONMENT="Staging" \
                            PLATFORM="Jenkins" \
                            FUNCTION=${STAGING_FUNCTION_NAME} \
                            VERSION=${GIT_COMMIT} \
                            BUILD_NUMBER=${BUILD_NUMBER}
                        ''')
            }
        }
        stage('Test Staging') {
            steps {
                sh('''#!/bin/bash
                        make testdeployment URL=${STAGING_URL}
                        ''')
            }
        }
        stage('Deploy Production') {
            steps {
                sh('''#!/bin/bash
                    make deploy \
                        ENVIRONMENT="Production" \
                        PLATFORM="Jenkins" \
                        FUNCTION=${PRODUCTION_FUNCTION_NAME} \
                        VERSION=${GIT_COMMIT} \
                        BUILD_NUMBER=${BUILD_NUMBER}                        
                        ''')
            }
        }
        stage('Test Production') {
            steps {
                sh('''#!/bin/bash
                    make testdeployment URL=${PRODUCTION_URL}

                        ''')
            }
        }
    }
}
