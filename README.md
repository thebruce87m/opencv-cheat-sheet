# opencv-cheat-sheet


Docker Build with Python and GStreamer:

```dockerfile

# nvidia base image to allow hardware decoding
FROM nvcr.io/nvidia/deepstream:6.3-gc-triton-devel

#
# GStreamer Dependences
#
RUN apt update && apt install -y \
\
libgstreamer-plugins-base1.0-dev \
libgstreamer1.0-dev \
libgstrtspserver-1.0-dev \
libx11-dev \
libjson-glib-dev \
\
libgstreamer1.0-dev \
libgstreamer-plugins-base1.0-dev \
libgstreamer-plugins-bad1.0-dev \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly \
gstreamer1.0-libav \
gstreamer1.0-tools \
gstreamer1.0-x \
gstreamer1.0-alsa \
gstreamer1.0-gl \
gstreamer1.0-gtk3 \
gstreamer1.0-qt5 \
gstreamer1.0-pulseaudio


#
# Install OpenCV4
#

WORKDIR /tmp/opencv/

# Install minimal prerequisites (Ubuntu 18.04 as reference)
RUN apt update && apt install -y \
\
cmake \
g++ \
wget \
unzip \
python3-dev \
python3-numpy

# Download and unpack sources
RUN wget -O opencv.zip https://github.com/opencv/opencv/archive/4.x.zip
RUN wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.x.zip
RUN unzip opencv.zip
RUN unzip opencv_contrib.zip

# Create build directory and switch into it
RUN mkdir -p build && cd build

WORKDIR /tmp/opencv/build/

# Configure
RUN cmake \
\
-D CMAKE_BUILD_TYPE=RELEASE \
\
-D WITH_GSTREAMER=ON \
-DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib-4.x/modules ../opencv-4.x \
\
\
-D BUILD_TESTS=OFF \
-D BUILD_PERF_TESTS=OFF \
-D BUILD_EXAMPLES=OFF \
-D BUILD_opencv_apps=OFF \
-D INSTALL_PYTHON_EXAMPLES=OFF \
-D INSTALL_C_EXAMPLES=OFF \
-D BUILD_DOCS=OFF \
\
-D BUILD_opencv_python2=OFF \
-D BUILD_opencv_python3=ON \
\
-D PYTHON3_EXECUTABLE=$(which python3) \
-D PYTHON3_INCLUDE_DIR=$(python3 -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") \
-D PYTHON3_LIBRARY=$(python3 -c "import sysconfig; print(sysconfig.get_config_var('LIBDIR'))") \
-D PYTHON3_NUMPY_INCLUDE_DIRS=$(python3 -c "import numpy; print(numpy.get_include())") \
-D PYTHON3_PACKAGES_PATH=$(python3 -c "import site; print([p for p in site.getsitepackages() if 'dist-packages' in p][0])") \
\
..


# Build
RUN cmake --build . -j$(nproc)

RUN make install

```
