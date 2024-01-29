#!/bin/groovy

pipeline {
    agent any
    
    options {
        disableConcurrentBuilds()
    }

    environment {
        KONG_ENVIRONMENT = "test"
        KONNECT_CONTROLPLANE_NAME = "cp-test-np"
    }

    stages {
        stage('Install Deck') {
            steps {
                script {
                    echo "Installing Deck"
                    sh '''
                        curl -sL https://github.com/kong/deck/releases/download/v1.30.0/deck_1.30.0_linux_amd64.tar.gz -o deck.tar.gz
                        tar -xf deck.tar.gz -C /tmp
                        cp /tmp/deck ~/deck
                    '''
                }
            }
        }

        stage('Sync Kong Configuration') {
            steps {
                script {
                    echo "Syncing Kong Configuration for ${KONG_ENVIRONMENT}"
                    // Ensure the directory containing deck is in the PATH
                    sh 'export PATH=$PATH:~/ && echo $PATH'

                    // Check if deck executable exists
                    sh 'which deck || echo "deck not found in PATH"'

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
