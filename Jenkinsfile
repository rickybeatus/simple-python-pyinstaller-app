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
  stage('Deploy') {
    try {
      docker.image('cdrx/pyinstaller-linux:python2').inside {
        sh 'docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-wilson_oey/sources:/src cdrx/pyinstaller-linux:python2 \'pyinstaller -F add2vals.py\''
      }
    } finally {
      if (currentBuild == 'SUCCESS') {
        echo 'This will always run'
        // archiveArtifacts 'dist/add2vals'
      }
    }
  }
}