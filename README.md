# About
This repository is forked from [willyd/caffe-builder](https://github.com/willyd/caffe-builder) with updated caffe third-party dependencies.  
#### Dependencies version
| lib | old version | new version |
| :--------: | :--------: | :--------: |
| boost | 1.61.0 | 1.68.0 |
| hdf5 | 1.8.16 | 1.8.17 |
| lmdb | 0.9.18 | 0.9.22 |
| opencv | 3.1.0 | 3.4.3 |
| protobuf | 3.1.0 | 3.6.1 |
| zlib | 1.2.8 | 1.2.11 |


It also aims to adapt builds for the currently latest **VS 2017**.  
Additionally, dependencies are downloaded together from netdisk to avoid some annoying network problems.  
Module build scripts also use local files in case of repeated downloads.


# Caffe-Builder
Caffe-Builder is a set of CMake scripts (using CMake's ExternalProject) that automate the build and installation of popular C/C++ open source libraries on Windows using microsoft toolchain.

Using this project will (hopefully) make building, installing and using open source libraries on Windows much easier.


## Building libraries
### Get the Prerequisites
* Third-party dependencies [Baidu Yun](https://pan.baidu.com/s/16Vj_4bUFKB9zgsThHSbeAA)
* CMake (3.12 or higher)
* Visual Studio (tested with 2017 in 64 bit mode)
* Git
* [Ninja](https://github.com/ninja-build/ninja/releases/download/v1.6.0/ninja-win.zip)

Make sure CMake, Git and Ninja are in your environment PATH.

### Optional dependencies
* CUDA (tested with 10.0) for GPU support in opencv and other libraries
* Python (tested with 3.6.5) to build boost.python for example

### Clone this repository:
    cmd> cd %caffe_builder_root%
    cmd> git clone https://github.com/codelilei/caffe-builder.git caffe-builder

### Building the libraries
    Download the prerequisites dependencies file mentioned above.
    Put it under the root directory of this repo and extract.
    Execute the following command to build the libraries.
    cmd> build_v150_x64.cmd

#### Alternatively to last step, you can execute one command after the other instead
    :: Create a build directory
    cmd> mkdir build && cd build

    :: Setup the VS compiler, Simply use vs2017 command prompt, or use common cmd and follow commands below
    :: for vs15 or earlier, e.g. VS 2013 cmd>  call "%VS120COMNTOOLS%....\VC\vcvarsall.bat" amd64
    :: set VS15COMNTOOLS to be the path where vcvarsall.bat is located
    cmd> set VS15COMNTOOLS="D:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\Common7\Tools\"
    cmd> call "%VS150COMNTOOLS%..\..\VC\Auxiliary\Build\vcvarsall.bat" amd64

    :: This will build all libraries that were configured to build with the BUILD_* options (see below)
    cmd> ninja
    :: Or alternatively
    cmd> ninja <libname>
    :: You can execute this command to list all possible targets
    cmd> ninja -t targets

The libraries will be installed in build\libraries. Alongside the libraries a series of Find*.cmake files will be installed and a caffe-builder-config.cmake file will be installed too. You can replace `150` with `140` in the commands above to build with VS 2015.

### Using the built libraries in your project
    cmd> cd myproject
    cmd> mkdir build
    cmd> cd build
    cmd> cmake -G <generator name> -C %caffe_builder_root%\caffe-builder\build\libraries\caffe-builder-config.cmake


# Known Issues
Sometimes hdf5 module will only be built for either debug or release mode.  
You can build the lost version via the following commands.
####
    cmd> cd build
    cmd> "%VS150COMNTOOLS%..\..\VC\Auxiliary\Build\vcvarsall.bat" amd64
    cmd> ninja build hdf5-release
