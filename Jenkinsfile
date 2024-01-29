#!/bin/groovy

BUILD_ENVIRONMENT = woodmac.getJenkinsEnvironment() == 'prod' ? 'iprod' : 'dev'
KONG_ENVIRONMENT = params.KONG_ENVIRONMENT

pipeline {
    parameters {
        choice(name: 'KONG_ENVIRONMENT', choices: (BUILD_ENVIRONMENT == "iprod" ? ['iuat', 'iprod'] : ["dev", "int"]), description: 'Kong Environment to Sync')
    }

    agent {
        ecs {
            inheritFrom "dynamic-us-east-1-${BUILD_ENVIRONMENT}"
        }
    }

    triggers {
    }

    options {
        disableConcurrentBuilds()
    }

    environment {
        KONG_ENVIRONMENT = "${params.KONG_ENVIRONMENT}"
        KONNECT_CONTROLPLANE_NAME = "cp-test-np"
    }

    stages {
        stage('Sync Kong Configuration') {
            // Previous configuration...
            when {
                anyOf {
                    branch 'main' // Multibranch pipeline (dev and int)
                    environment(name: "GIT_BRANCH", value: "origin/main") // Single branch pipeline (uat and prod)
                }
            }
            steps {
                sh(script: '''#!/bin/bash
                    # Perform necessary tasks for syncing Kong configuration
                    echo "Syncing Kong Configuration for ${KONG_ENVIRONMENT}"
                    # Your synchronization logic here
                    export DECK_KONNECT_TOKEN=kpat_ki3SNw038BdTMWRxoK9U7iNTfBeKDl13LmsHCAvGlMbQ7IBIR
                    deck sync \
                        -e $KONG_ENVIRONMENT \
                        -s $(pwd)/kong-config/kong.yaml \
                        --konnect-control-plane-name $KONNECT_CONTROLPLANE_NAME
                ''', label: "Sync configuration")
            }
        }
    }
}
