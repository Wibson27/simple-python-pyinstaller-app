node {
    def commitHash
    
    stage('Checkout') {
        checkout scm
        commitHash = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        echo "Building commit: ${commitHash}"
    }
    
    stage('Build') {
        docker.image('python:3.11-alpine').inside {
            sh '''
                echo "Compiling Python source to bytecode..."
                python -m py_compile sources/add2vals.py sources/calc.py
                echo "Build completed successfully!"
            '''
        }
    }
    
    stage('Test') {
        docker.image('python:3.11-alpine').inside {
            sh '''
                echo "Installing pytest..."
                pip install pytest
                echo "Running unit tests..."
                py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py
            '''
        }
        
        // Always publish test results, even if tests fail
        junit 'test-reports/results.xml'
    }
}