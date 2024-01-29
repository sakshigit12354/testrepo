#!/bin/groovy

BUILD_ENVIRONMENT = woodmac.getJenkinsEnvironment() == 'prod' ? 'iprod' : 'dev'
KONG_ENVIRONMENT = params.KONG_ENVIRONMENT

pipeline {
    agent any
    
    parameters {
        choice(name: 'KONG_ENVIRONMENT', choices: (BUILD_ENVIRONMENT == "iprod" ? ['iuat', 'iprod'] : ["dev", "int"]), description: 'Kong Environment to Sync')
    }

    options {
        disableConcurrentBuilds()
    }

    environment {
        KONNECT_CONTROLPLANE_NAME = "cp-test-np"
    }

    stages {
        stage('Sync Kong Configuration') {
            when {
                anyOf {
                    branch 'main' // Multibranch pipeline (dev and int)
                    environment(name: "GIT_BRANCH", value: "origin/main") // Single branch pipeline (uat and prod)
                }
            }
            steps {
                script {
                    echo "Syncing Kong Configuration for ${KONG_ENVIRONMENT}"
                    // Your synchronization logic here
                    sh(script: '''#!/bin/bash
                        export DECK_KONNECT_TOKEN=kpat_ki3SNw038BdTMWRxoK9U7iNTfBeKDl13LmsHCAvGlMbQ7IBIR
                        deck sync \
                            -e $KONG_ENVIRONMENT \
                            -s $(pwd)/kong-config/kong.yaml \
                            --konnect-control-plane-name $KONNECT_CONTROLPLANE_NAME
                    ''')
                }
            }
        }
    }
}
