node {
  stage('Build') {
    docker.image('python:2-alpine').inside {
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
    }
  }

  stage('Test') {
    try {
      docker.image('qnib/pytest').inside {
        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
      }
    } finally {
      junit 'test-reports/results.xml'
    }
  }

  stage('Manual Approval') {
    input(message:"Lanjutkan ke tahap Deploy?")
  }

  stage('Deploy') {
    withEnv(["VOLUME=${pwd()}/sources:/src",
             'IMAGE=cdrx/pyinstaller-linux:python2']) {
      try {
          sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller --onefile add2vals.py'" 
      } finally {
        if (currentBuild == 'SUCCESS') {
          archiveArtifacts artifacts: "sources/dist/add2vals"
        }
        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
        // sleep time: 1, unit: 'MINUTES'
      }  
    }
  }
}