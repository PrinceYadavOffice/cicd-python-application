pipeline {
    agent any

    environment {
        VENV = "venv"
    }

    stages {
        stage('Checkout') {
            steps {
                // Get code from Git
                checkout scm
            }
        }

        stage('Setup venv & Install deps') {
            steps {
                sh '''
                    python3 -m venv ${VENV}
                    . ${VENV}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run tests') {
            steps {
                sh '''
                    . ${VENV}/bin/activate
                    export PYTHONPATH=$PWD
                    pytest
                '''
            }
        }

        stage('Deploy') {
            when {
                // Only deploy on main branch (optional, but good practice)
                branch 'main'
            }
            steps {
                sh '''
                    echo "=== Deploying app to deploy/ directory ==="
                    mkdir -p "$WORKSPACE/deploy"

                    # Copy main app files
                    cp app.py math_utils.py "$WORKSPACE/deploy/" || true
                    cp requirements.txt "$WORKSPACE/deploy/" || true

                    # Create a small run script
                    cat > "$WORKSPACE/deploy/run.sh" << 'EOF'
#!/usr/bin/env bash
python3 app.py
EOF

                    chmod +x "$WORKSPACE/deploy/run.sh"

                    echo "Deployment complete. To run the app:"
                    echo "  cd $WORKSPACE/deploy && ./run.sh"
                '''
            }
        }
    }

    post {
        always {
            echo "Build finished. (success or fail)"
        }
        success {
            echo "Build succeeded 🎉"
        }
        failure {
            echo "Build failed ❌"
        }
    }
}
