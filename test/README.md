# Untect-Doorlock
graduation project

## test scrips: ##
### arduino test ###
- arduino.cpp
- arduino.py

* * *

### rasberry pi test 1: Installation
#### 1. Mediapipe Installation

 [Mediapipe] (https://google.github.io/mediapipe/getting_started/install#installing-on-debian-and-ubuntu)

 ```
 $ sudo apt-get update
 $ sudo apt-get upgrade

 $ cd ~
 $ git clone https://github.com/google/mediapipe.git

 $ cd mediapipe
 ```


#### 2. OpenCV, FFmpeg Installation

 ```
 $ cd ~/mediapipe
 $ chmod +x setup_opencv.sh
 $ ./setup_opencv.sh
 ```


#### 3. bazel Installation

 [bazel git] (https://github.com/PINTO0309/Bazel_bin)

 - ver: 2.0
 - install jdk8
 - 레파지토리에 포함된 bazel설치를 복제하는 스크립트를 실행

 ```
 $ sudo apt-get install -y openjdk-8-jdk

 $ cd ~
 $ git clone https://github.com/PINTO0309/Bazel_bin.git
 $ cd Bazel_bin/2.0.0/Raspbian_Debian_Buster_armhf/openjdk-8-jdk/
 $ ./install.sh
 ```

 - bazel이라는 파일이 /usr/local/bin에 추가
 - 다음 명령을 실행하여 bazel 메뉴가 뜬다면 정상 설치 
 
 ```
 $ /usr/local/bin/bazel
 ```
 
#### 4. EGL driver Installation to run Mediapipe on GPU

  ```
  $ sudo apt-get install mesa-common-dev libegl1-mesa-dev libgles2-mesa-dev -y
  ```


#### 5. run Mediapipe

  - mediapipe/examples/desktop/hello_world/BUILD 에 cc_binary를 추가

 ```
 linkopts = [
     "-latomic"
 ]
 ```

 - Mediapipe의 Hello World를 실행

 ```
 $ cd ~/mediapipe
 $ export GLOG_logtostderr=1
 $ bazel run --copt -DMESA_EGL_NO_X11_HEADERS --copt -DEGL_NO_X11 \
     mediapipe/examples/desktop/hello_world:hello_world
  ```

  - bazel을 구축한 후(수십 분이 소요 됩니다) 'Hello World!'가 10번 표시되면 정상

### rasberry pi test 2: Hand Tracking
#### 1. add _cc_binary_
 - path: mediapipe/examples/desktop/multi_hand_tracking/BUILD
 ```
 linkopts = [
    "-latomic",
    "-lopencv_core",
    "-lopencv_highgui",
    "-lopencv_imgproc",
    "-lopencv_videoio"
],
 ```


