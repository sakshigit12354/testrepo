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
        stage('Sync Kong Configuration') {
            when {
                anyOf {
                    branch 'main' // Multibranch pipeline (dev and int)
                    environment(name: "GIT_BRANCH", value: "origin/main") // Single branch pipeline (uat and prod)
                }
            }
            steps {
                script {
                    echo "Installing Deck"
                    sh '''
                        curl -sL https://github.com/kong/deck/releases/download/v1.30.0/deck_1.30.0_linux_amd64.tar.gz -o deck.tar.gz
                        tar -xf deck.tar.gz -C /tmp
                        cp /tmp/deck /var/jenkins_home/deck
                    '''
                    echo "Syncing Kong Configuration for ${KONG_ENVIRONMENT}"
                    // Your synchronization logic here
                    
                    sh 'ls -l /var/jenkins_home/deck || echo "deck not found in specified location"'

                    sh(script: '''#!/bin/bash
                        export DECK_KONNECT_TOKEN=kpat_ki3SNw038BdTMWRxoK9U7iNTfBeKDl13LmsHCAvGlMbQ7IBIR
                        /var/jenkins_home/deck sync \
                            -s $(pwd)/kong-config/kong.yaml \
                            --konnect-control-plane-name $KONNECT_CONTROLPLANE_NAME
                    ''')
                }
            }
        }
    }
}
