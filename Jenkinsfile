pipeline {
  agent none
  options {
    disableConcurrentBuilds()
    buildDiscarder(logRotator(numToKeepStr: '8', daysToKeepStr: '20'))
    timeout(time: 1, unit: 'HOURS')
  }
  stages {
    stage('torch') {
      agent {
	dockerfile {
	  dir 'tools'
	  args '--device /dev/nvidia0:/dev/nvidia0 --device /dev/nvidiactl:/dev/nvidiactl --device /dev/nvidia-uvm:/dev/nvidia-uvm'
	}
      }
      environment {
	HOME = pwd(tmp:true)
      }
      steps {
	sh 'python3 -m venv $HOME'
	sh '''#!/bin/bash -ex
	  source $HOME/bin/activate
	  pip3 install -r requirements.txt pytest torchvision
	  python3 setup.py develop
	  KYMATIO_BACKEND=$STAGE_NAME pytest
	'''
      }
    }
    stage('skcuda') {
      agent {
	dockerfile {
	  dir 'tools'
	  args '--device /dev/nvidia0:/dev/nvidia0 --device /dev/nvidiactl:/dev/nvidiactl --device /dev/nvidia-uvm:/dev/nvidia-uvm'
	}
      }
      environment {
	HOME = pwd(tmp:true)
      }
      steps {
	sh 'python3 -m venv $HOME'
	sh '''#!/bin/bash -ex
	  source $HOME/bin/activate
	  pip3 install -r requirements.txt pytest scikit-cuda cupy
	  python3 setup.py develop
	  KYMATIO_BACKEND=$STAGE_NAME pytest
	'''
      }
    }
  }
}