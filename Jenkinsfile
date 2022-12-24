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
      VOLUME = '/var/jenkins_home/workspace/submission-cicd-pipeline-ricky_ritonga/sources:/src'
      IMAGE = 'cdrx/pyinstaller-linux:python2'
    }
    try {
      sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller --onefile sources/add2vals.py'"
    } finally {
      // if (currentBuild == 'SUCCESS') {
        archiveArtifacts artifacts: "4/sources/dist/add2vals", fingerprint: true
        // archiveArtifacts artifact "${env.BUILD_ID}/sources/dist/add2vals"
        // sh "docker run --rm -v /var/jenkins_home/workspace/submission-cicd-pipeline-ricky_ritonga/sources:/src cdrx/pyinstaller-linux:python2 'rm -rf build dist'"
      // }
    }  
  }
}