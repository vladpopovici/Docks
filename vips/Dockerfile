FROM alpine:3.13

RUN set -ex  && \
	apk --no-cache --update add \
		bash bash-completion coreutils file grep openssl openssh\
		vim nano curl sudo tar xz build-base cmake git \
		binutils man-db bash-doc man-pages less less-doc \
		font-noto shadow g++ make mc tmux

RUN set -ex  && \
	apk --no-cache --update add \
		automake autoconf libtool zlib-dev jpeg-dev \
		openjpeg-dev tiff-dev glib-dev libpng-dev \
		freetype-dev gdk-pixbuf-dev libxml2-dev \
		sqlite-dev expat-dev openexr-dev openexr-tools \
		libgsf-dev libexif-dev libimagequant-dev
#
# Need to use PIXMAN-0.36.0 for .MRXS files to load properly.
# Therefore, some dependencies need to be rebuild on PIXMAN-0.36.0...
#

RUN set -ex && \ 
	mkdir -p /root/build && \
	cd /root/build  && \
	wget https://www.cairographics.org/releases/pixman-0.36.0.tar.gz  && \
	tar xzf pixman-0.36.0.tar.gz && \
	cd pixman-0.36.0 && \
	./configure  && \
	make -j4 install && \
	cd .. && \
	rm -Rf pixman*

RUN set -ex && \ 
	mkdir -p /root/build  && \
	cd /root/build  && \
	wget https://www.cairographics.org/releases/cairo-1.16.0.tar.xz && \
	xzdec cairo-1.16.0.tar.xz | tar xf - && \
	cd cairo-1.16.0 && \
	./configure && \
	make -j4 install && \
	cd .. && \
	rm -Rf cairo*


RUN set -ex && \
	mkdir -p /root/build  && \
	cd /root/build  && \
	git clone https://github.com/openslide/openslide.git && \
	cd openslide && \
	autoreconf -i && \
	./configure && \
	make -j4 install && \
	cd .. && \
	rm -Rf openslide* 

RUN set -ex && \
	mkdir -p /root/build  && \
	cd /root/build  && \
	wget https://github.com/libvips/libvips/releases/download/v8.10.5/vips-8.10.5.tar.gz && \
	tar xzf vips-8.10.5.tar.gz && \
	cd vips-8.10.5 && \
	./configure && \
	make -j4 install && \
	cd .. && \
	rm -Rf vips* 

# local user
ENV USERNAME devel
RUN set -ex && \
	useradd --create-home --key MAIL_DIR=/dev/null --shell /bin/bash $USERNAME && \
	passwd -d $USERNAME

# Set timezone (default = UTC)
ENV TZ Europe/Vienna
RUN set -ex && \
	apk add tzdata && \
	cp /usr/share/zoneinfo/$TZ /etc/localtime && \
	echo "$TZ" > /etc/timezone && \
	date

