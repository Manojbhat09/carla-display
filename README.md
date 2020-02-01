
# Carla Display in Web Browser

This repo is used for displaying Carla in a web browser (i.e. Chrome, Safari...) to realize visulization of Carla in a different (or same) machine. 

## UPDATES !!!!

#### [01/31/2020]

Now you can use docker to run the backend along with frontend. Please refer this [instruction](#docker). Since container has been provided, all newest features like new xviz lib will be merged into master.

#### [01/10/2020]
Since container has been provided, all newest features like new xviz lib will be merged into master.

~~I changed some codes in a backend branch (currently not merged into backend's master) using my xviz cpp library. Please check following codes for more information if you want to have a try.~~

~~Note: If you are new to this repo OR you do not have Protobuf 3.11.0+ installed, please just ignore this update and don't try it.~~

~~1. New cpp backend with xviz cpp library. Please check out to branch ["test_new_xviz_lib"](https://github.com/mellocolate/carla-display-backend/tree/test_new_xviz_lib) if you want to have a try.~~
~~2. XVIZ Cpp implementation library [xviz](https://github.com/wx9698/xviz)~~

 
#### [11/20/2019]

Yes! I added support for Ubuntu 16.04 with a python3 backend!

Ubuntu 16.04: [python3 backend](https://github.com/mellocolate/carla-display-backend-python) + [frontend](https://github.com/mellocolate/carla-display-frontend)

Ubuntu 18.04: [c++](https://github.com/mellocolate/carla-display-backend) or [python3](https://github.com/mellocolate/carla-display-backend-python) backend + [frontend](https://github.com/mellocolate/carla-display-frontend)

Now the this system can be used either on Ubuntu 16.04 with a python3 backend or on Ubuntu 18.04 with a c++ backend or a python3 backend

Currently only compatible with Carla 0.9.6 version

Demo use case from [Wenhao Ding](https://github.com/GilgameshD)
![](images/example_2.gif)

![](images/example.gif)

## Source Code

[Backend(cpp)](https://github.com/mellocolate/carla-display-backend)

[Backend(python)](https://github.com/mellocolate/carla-display-backend-python)

[Frontend](https://github.com/mellocolate/carla-display-frontend) (Adapted from Uber [streetscape.gl](https://github.com/uber/streetscape.gl))

## Use case
1. Running carla simulator in the cloud and visualize it in your local machine's web browser.
2. Draw something (i.e. trajectories) in the web browser.
3. TBD

## <a name="docker"></a> Docker

If you don't want to build yourself, using a container is a good choice.

And both Ubuntu 16 and Ubuntu 18 can use program in the container.

```bash
# first pull the docker file, you may need sudo
docker pull mellocolate/carla-display:0.9.6

# run your carla server
cd CARLA_SIMULATOR_PATH
./CarlaUE4.sh

# then in another terminal run container
# NOTICE: if you are running carla server and this container remotely in the cloud, please replace
# CARLA_DISPLAY_HOST_IP=localhost with CARLA_DISPLAY_HOST_IP=YOUR_REMOTE_MACHINE_PUBLIC_IP
docker run -it --network="host" -e CARLA_DISPLAY_HOST_IP=localhost mellocolate/carla-display:0.9.6

# then you can type "0.0.0.0:8080" or "YOUR_REMOTE_MACHINE_PUBLIC_IP:8080" in your browser
# to see the display if your browser is not prompted automatically.

# now you can run your own python client to see what will happen in the browser!
cd CARLA_SIMULATOR_PATH/PythonAPI/examples
python3 spawn_npc.py

# NOTICE: before you terminate the container, remember first to terminate all your python scripts.

```

---------------------------------------------------
---------------------------------------------------
---------------------------------------------------

## Prerequisites

Please refer this [instruction](https://github.com/protocolbuffers/protobuf/blob/master/src/README.md) to install protobuf (>3.10.0)

## Build

### Step 1 - Backend Part
Please choose anyone of the backend. 

Notice: Ubuntu 16.04 can only run python backend

#### Python

Following steps are tested on both Ubuntu 16.04 and Ubuntu 18.04 with Carla 0.9.6

Notice: backend should be launched after the carla simulator has been launched
```bash
# install system libs and building utils
$ sudo apt install -y make gcc g++ libpng16-dev libjpeg-dev libtiff5-dev python3-pip

# install python3 packages
$ pip3 install numpy easyDict protobuf shapely

# clone the python backend repo
$ git clone https://github.com/mellocolate/carla-display-backend-python.git
```

#### C++
Following building steps are ONLY tested on Ubuntu 18.04 with Carla 0.9.6

Notice: backend should be launched after the carla simulator has been launched
```bash
# install make gcc g++
$ sudo apt install -y make gcc g++

# git clone backend repo
$ git clone https://github.com/mellocolate/carla-display-backend.git 

# install some pre-build packages and libraries
$ cd carla-display-backend

# install necessary carla build tools
$ sudo ./setup/install-carla-tool.sh

# download boost, rpc, gtest and recast lib to this client folder
$ ./setup/setup.sh                               
# you can use command "./setup/setup.sh clang" to indicate script to build with clang

# build
$ mkdir build && cd build
$ cmake ../
# you can also use command "cmake -DUSE_CLANG=ON ../" to indicate cmake to set default compiler to clang

# make executable, replace the number with your number of cores to achieve faster building speed
$ make platform -j8                                    
# it will generate the binary program at REPO_ROOT_FOLDER/bin
```

To use this system, you MUST build this frontend part
### Step 2 - Frontend part

In another terminal

frontend part (should be in the same machine of the carla simulator)
```bash
# install make gcc g++
$ sudo apt install -y make gcc g++

# install nodejs 10.x
$ curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
$ sudo apt-get install -y nodejs

# install yarn
$ curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
$ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
$ sudo apt-get update && sudo apt-get install -y yarn

# git clone and launch frontend
$ git clone https://github.com/mellocolate/carla-display-frontend.git

# install dependencies
$ cd carla-display-frontend
$ yarn

```

## How to use?
```bash
# Step 1
# In one terminal
# go into your compiled carla simulator's folder
$ cd CARLA_SIMULATOR_PATH
$ ./CarlaUE4.sh

# Step 2
# In another terminal
# the backend should be launched after launching the carla simulator

# YOU MAY CHOOSE THE BACKEND YOU BUILT

# Python backend
$ cd carla-display-backend-python
$ python3 backend/main.py

# C++ backend
# go into the backend folder
$ cd carla-display-backend
# to launch the backend, type the following command
$ ./bin/platform


# Step 3
# In another terminal
# go into the frontend folder
$ cd carla-display-frontend
# start frontend 
# if you are using these carla, backend and frontend remotely in the cloud,
# please set env variable CARLA_DISPLAY_HOST_IP to YOUR_REMOTE_MACHINE_PUBLIC_IP
# before running yarn start-live
$ yarn start-live
# open your browser and type "0.0.0.0:8080" if you are running all these locally.
# or "YOUR_REMOTE_MACHINE_PUBLIC_IP:8080" if you are running all these remotely.


# Step 4
# In another terminal
# run your own python client script
$ cd CARLA_SIMULATOR_PATH/PythonAPI/examples
$ python spawn_npc.py
```

## Third party libraries
1. [Uber streetscape.gl](https://github.com/uber/streetscape.gl) as frontend
2. [nlohmann json](https://github.com/nlohmann/json)
3. [ReneNyffenegger cpp-base64](https://github.com/ReneNyffenegger/cpp-base64)
4. [lvandeve lodepng](https://github.com/lvandeve/lodepng)
5. [mcrodrigues macro-logger](https://github.com/dmcrodrigues/macro-logger)
6. [jessey-git fx-gltf](https://github.com/jessey-git/fx-gltf)
7. [mjxu96 xviz](https://github.com/wx9698/xviz)
8. [cmpute xviz.py](https://github.com/cmpute/xviz.py)