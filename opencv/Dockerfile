FROM okwrtdsh/rpi-ffmpeg:latest

ARG NUM_CORES=2
ARG OPENCV_VERSION=3.4.4
ARG GSL_VERSION=2.5
ARG VLFEAT_VERSION=0.9.21

RUN apt-get update -qq \
 && apt-get upgrade -y \
 && apt-get install --no-install-recommends -y \
# opencv3 requirements
	libgtk2.0-dev libavcodec-dev libavformat-dev libswscale-dev \
	libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev \
# libjasper-dev
# opencv3 extra
	libtiff5-dev \
	libv4l-dev v4l-utils libmp3lame-dev \
	libboost-all-dev libeigen3-dev \
# libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
	libglib2.0-0 libxext6 libsm6 libxrender1 libxvidcore-dev libatlas-base-dev gfortran \
# libpng12-dev libx264
 && cd /tmp \
 && wget -O opencv.tar.gz https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.tar.gz \
 && tar -zxf opencv.tar.gz \
 && wget -O opencv_contrib.tar.gz https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.tar.gz \
 && tar -zxf opencv_contrib.tar.gz \
 && pip --no-cache-dir install \
	numpy scipy \
 && cd /tmp/opencv-${OPENCV_VERSION} \
 && mkdir build \
 && cd build \
 && cmake \
	-D CMAKE_BUILD_TYPE=RELEASE \
	-D CMAKE_INSTALL_PREFIX=/usr/local \
	-D WITH_TBB=ON \
	-D BUILD_PYTHON_SUPPORT=ON \
	-D WITH_V4L=ON \
	-D INSTALL_C_EXAMPLES=ON \
	-D INSTALL_PYTHON_EXAMPLES=ON \
	-D BUILD_EXAMPLES=ON \
	-D BUILD_DOCS=ON \
	-D OPENCV_EXTRA_MODULES_PATH=/tmp/opencv_contrib-${OPENCV_VERSION}/modules \
	-D WITH_XIMEA=YES \
#	-D WITH_QT=YES \
	-D WITH_FFMPEG=YES \
	-D WITH_PVAPI=YES \
	-D WITH_GSTREAMER=YES \
	-D WITH_TIFF=YES \
	-D WITH_OPENCL=YES \
	-D BUILD_opencv_python3=ON \
	-D PYTHON3_EXECUTABLE=/usr/local/bin/python3 \
	-D PYTHON3_INCLUDE_DIR=/usr/local/include/python3.7m/ \
	-D PYTHON3_LIBRARY=/usr/local/lib/libpython3.so \
	-D PYTHON_LIBRARY=/usr/local/lib/libpython3.so \
	-D PYTHON3_PACKAGES_PATH=/usr/local/lib/python3.7/site-packages/ \
	-D PYTHON3_NUMPY_INCLUDE_DIRS=/usr/local/lib/python3.7/site-packages/numpy/core/include/ \
	.. \
 && make -j ${NUM_CORES} \
 && make install \
 && make clean \
 && sh -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf' \
\
# GSL 2.5
 && cd /tmp \
 && curl -L ftp://ftp.gnu.org/gnu/gsl/gsl-${GSL_VERSION}.tar.gz | tar zxf - \
 && cd gsl-${GSL_VERSION} \
 && ./configure \
 && make -j ${NUM_CORES} \
 && make install \
 && make clean \
\
# VLFeat
 && cd /usr/local/src \
 && wget -O vlfeat.tar.gz http://www.vlfeat.org/download/vlfeat-${VLFEAT_VERSION}-bin.tar.gz \
 && tar -zxf vlfeat.tar.gz \
 && rm vlfeat.tar.gz \
 && echo "export VLFEAT_DIR=/usr/local/src/vlfeat-${VLFEAT_VERSION}" > /etc/profile.d/vlfeat.sh \
\
# fix libdc1394 error: Failed to initialize libdc1394
 && ln /dev/null /dev/raw1394 \
 && mkdir -p /etc/udev/rules.d/ \
 && echo 'KERNEL=="raw1394", GROUP="video"' > /etc/udev/rules.d/raw1394.rules \
\
 && ldconfig \
 && apt-get remove --purge -y curl build-essential checkinstall cmake \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

CMD ['pkg-config', '--modversion', 'opencv']
