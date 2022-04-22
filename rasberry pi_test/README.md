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

### rasberry pi test 2: Multi Hand Tracking
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

#### 2. link camera
  - add this option if no camera
  ```
  bazel-bin/mediapipe/examples/desktop/multi_hand_tracking/multi_hand_tracking_gpu \
   --calculator_graph_config_file=mediapipe/graphs/hand_tracking/multi_hand_tracking_desktop_live.pbtxt \
   --input_video_path="video.mp4" \
   --output_video_path="video_gpu.mp4"
  ```
  
 #### try- build Mediapipe
  - Multi Hand Tracking on CPU
  ```
  $ cd ~/mediapipe

  $ bazel build -c opt --define MEDIAPIPE_DISABLE_GPU=1 \ mediapipe/examples/desktop/multi_hand_tracking:multi_hand_tracking_gpu

  $ export GLOG_logtostderr=1
  $ bazel-bin/mediapipe/examples/desktop/multi_hand_tracking/multi_hand_tracking_gpu \ --calculator_graph_config_file=mediapipe/graphs/hand_tracking/multi_hand_tracking_desktop_live.pbtxt
  ```
  
 - Multi Hand Tracking on GPU
 ```
 $ cd ~/mediapipe

 $ bazel build -c opt --copt -DMESA_EGL_NO_X11_HEADERS --copt -DEGL_NO_X11 \ mediapipe/examples/desktop/multi_hand_tracking:multi_hand_tracking_gpu

 $ export GLOG_logtostderr=1
 $ bazel-bin/mediapipe/examples/desktop/multi_hand_tracking/multi_hand_tracking_gpu \ --calculator_graph_config_file=mediapipe/graphs/hand_tracking/multi_hand_tracking_desktop_live.pbtxt
 ```

#### 3. adjust files in path: mediapipe/examples/desktop
- demo_run_graph_main_gpu.cc
- end_loop_calculator.h
- landmarks_to_render_data_calculator.cc
 
 #### 4. adjuct _cc_binary_ in path: mediapipe/examples/desktop/BUILD
 ```
 cc_library(
	name = "demo_run_graph_main_gpu",
    srcs = ["demo_run_graph_main_gpu.cc"],
    deps = [ 
    	"//mediapipe/framework:calculator_framework",
    	"//mediapipe/framework/formats:image_frame",
        "//mediapipe/framework/formats:image_frame_opencv",
        "//mediapipe/framework/port:commandlineflags",
        "//mediapipe/framework/port:file_helpers",
        "//mediapipe/framework/port:opencv_highgui",
        "//mediapipe/framework/port:opencv_imgproc",
        "//mediapipe/framework/port:opencv_video",
        "//mediapipe/framework/port:parse_text_proto",
        "//mediapipe/framework/port:status",
        "//mediapipe/gpu:gl_calculator_helper",
        "//mediapipe/gpu:gpu_buffer",
        "//mediapipe/gpu:gpu_shared_data_internal",
        "//mediapipe/framework/formats:landmark_cc_proto",
    ],
)
 ```

#### 5. extraxt landmark
 - use _build.py_ to get _landmark.txt_
