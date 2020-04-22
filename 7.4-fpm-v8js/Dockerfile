FROM devwithlando/php:7.4-fpm-2

RUN apt-get update -y \
  && apt-get install -y \
  build-essential \
  curl \
  git \
  xxd \
  python \
  libglib2.0-dev \
  libtinfo-dev \
  libtinfo5 \
  software-properties-common \
  patchelf \
  # Build V8
  && cd /tmp \
  && git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git \
  && export PATH=`pwd`/depot_tools:"$PATH" \
  && fetch v8 \
  && cd v8 \
  && git checkout 7.9-lkgr \
  && gclient sync \
  && tools/dev/v8gen.py -vv x64.release -- is_component_build=true use_custom_libcxx=false \
  && ninja -C out.gn/x64.release/ \
  && mkdir -p /opt/v8/lib \
  && mkdir -p /opt/v8/include \
  && cp out.gn/x64.release/lib*.so out.gn/x64.release/*_blob.bin out.gn/x64.release/icudtl.dat /opt/v8/lib/ \
  && cp -R include/* /opt/v8/include/ \
  && for A in /opt/v8/lib/*.so; do patchelf --set-rpath '$ORIGIN' $A; done \
  # Build v8js
  && cd /tmp \
  && git clone https://github.com/phpv8/v8js.git \
  && cd v8js \
  && git checkout clean-timezone-fix \
  && phpize \
  && ./configure --with-v8js=/opt/v8 LDFLAGS="-lstdc++" \
  && make \
  && make install \
  && docker-php-ext-enable v8js
