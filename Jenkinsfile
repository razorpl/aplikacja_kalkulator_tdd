pipeline {
    agent any
    parameters {
       gitParameter branchFilter: 'origin/(.*)', defaultValue: 'main', name: 'BRANCH', type: 'PT_BRANCH'
       string(name: 'LEVEL', defaultValue: 'smoke', description: '')
    //
    }
    stages {
        stage('CHECKOUT') {
            steps {
                script {
                    git branch: "${params.BRANCH}", url: 'https://github.com/razorpl/aplikacja_kalkulator_tdd.git'
                    sh 'echo Checkout'
                    checkout([$class: 'GitSCM', branches: [[name: '$BRANCH']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/razorpl/aplikacja_kalkulator_tdd.git']]])
                }
            }
        }
        stage('STATIC CODE TEST') {
            steps {
                script {
                    sh 'echo Static code testing with pylint'
                    sh 'pylint *.py **/*.py'                    
                }
            }
        }
        stage('MAIN TEST') {
            parallel {
                stage ('SUBTEST 1') {
                    when { expression { params.LEVEL == 'smoke' }}
                    steps {
                        script {
                            sh 'echo Smoke testing'
                            sh 'python3.8 -m pytest smoke --html=report.html'
                            archiveArtifacts artifacts: 'index.html, assets/', followSymlinks: false
                        }
                    }
                }
                stage ('SUBTEST 2') {
                    when { expression { params.LEVEL == 'regression' }}
                    steps {
                        script {
                            sh 'echo Run Pytest'
                            sh 'python3.8 -m pytest smoke --html=report.html'
                            sh 'python3.8 -m pytest --html=report.html'
                            archiveArtifacts artifacts: 'index.html, assets/', followSymlinks: false
                        }
                    }
                }
                stage ('SUBTEST 3') {
                    when { expression { params.LEVEL == 'nightly' }}
                    steps {
                        script {
                            sh 'echo Run Pytest'
                            sh 'sleep 30'
                            sh 'python3.8 -m pytest --html=report.html'
                            archiveArtifacts artifacts: 'index.html, assets/', followSymlinks: false
                        }
                    }
                }
                
                
                
                
                
                
            }
        }
        stage('CLEANUP') {
            steps {
                script {
                    sh 'echo Archive'
                    archiveArtifacts artifacts: 'index.html, assets/', followSymlinks: false
                    deleteDir()
                }
            }
        }
    }
}

def testLevel() {
   when { 
      expression { 
         params.LEVEL == 'smoke'
      }
   }
}
