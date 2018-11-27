FROM okwrtdsh/rpi-core:latest

ARG NUM_CORES=2
ARG FFMPEG_VERSION=n4.1

RUN apt-get update -qq \
 && apt-get upgrade -y \
 && apt-get install --no-install-recommends -y \
# base
	curl wget zip unzip git bzip2 ca-certificates mercurial subversion imagemagick \
# ffmpeg
	autoconf automake build-essential cmake checkinstall  g++ yasm  \
	libass-dev libfreetype6-dev libsdl2-dev libtool libva-dev \
	libvdpau-dev libvorbis-dev libxcb1-dev libxcb-shm0-dev libxcb-xfixes0-dev \
	pkg-config texinfo zlib1g-dev \
\
 && cd /tmp \
 && wget -O ffmpeg.tar.gz https://github.com/FFmpeg/FFmpeg/archive/${FFMPEG_VERSION}.tar.gz \
 && tar -zxvf ffmpeg.tar.gz \
\
# build ffmpeg
 && cd /tmp/FFmpeg-${FFMPEG_VERSION} \
 && ./configure --enable-shared --disable-static --enable-nonfree \
 && make -j ${NUM_CORES} \
 && make install \
 && make clean \
 && ldconfig \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

CMD ['ffmpeg', '-version']
