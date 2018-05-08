#!groovy

def target_cluster = "10.65.182.143"

def BuildJob(projectName) {
    try {
       stage(projectName) {
         node {      
           def res = build job:projectName, propagate: false
           result = res.result

           if (result.equals("SUCCESS")) {
           } else {
              error 'FAIL' // this fails the stage
           }
         }
       }
    } catch (e) {
        currentBuild.result = 'UNSTABLE'
        result = "FAIL" // make sure other exceptions are recorded as failure too
    }
}

node {
    stage('installation') {
      sh "python2.7 /var/tmp/Nightswatch/deploy/upgrade_machines_sa.py -p $target_cluster --ininame \'/var/lib/jenkins/nightswatch/5.1/config.ini\'"
    }

    BuildJob('multiple_pods_basic_functionality_5.1')

    BuildJob('multiple_pods_session_allocation_5.1')

    stage('copy xmls') {
      sh "scp -p root@$target_cluster:/tmp/multiple_pods/*.xml ."
    }

    stage('check_cores') {
      sh "ssh root@$target_cluster 'python2.7 /var/Nightswatch/deploy/find_cores.py'"
    }

    stage('publish junit results') {
      junit(testResults: '*.xml', healthScaleFactor: 1.0, allowEmptyResults: true)
    }
}