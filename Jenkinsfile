// node {
//   withDockerContainer('python:2-alpine') {
//     stage('Build') {
//       sh 'python -m py_compile sources/add2vals.py sources/calc.py'
//     }
//   }
//   withDockerContainer('qnib/pytest') {
//     stage('Test') {
//       sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
//       junit 'test-reports/results.xml'
//     }
//   }
// }

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
    environment {
      VOLUME = '$(pwd)/sources:/src'
      IMAGE = 'cdrx/pyinstaller-linux:python2'
      PATH = 'test123'
    }
    try {
        // unstash(name: 'compiled-results') 
        echo PATH
        sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller --onefile sources/add2vals.py'" 
      // sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller --onefile sources/add2vals.py'"
    } finally {
      // if (currentBuild == 'SUCCESS') {
        echo PATH
        // archiveArtifacts artifacts: "${PATH}/sources/dist/add2vals"
        // archiveArtifacts artifact "${env.BUILD_ID}/sources/dist/add2vals"
        sh "docker run --rm -v $(pwd)/sources:/src ${IMAGE} 'rm -rf build dist'"
      // }
    }  
  }
}