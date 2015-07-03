# Start with CUDA Torch base image
FROM kaixhin/cuda-torch
MAINTAINER Kai Arulkumaran <design@kaixhin.com>

# Move cuDNN over and extract
ADD cudnn*.tgz root/
# Install cuDNN
RUN cd root/cudnn* && \
  cp *.h /usr/local/cuda/include/ && \
  cp *.so* /usr/local/cuda/lib64/ && \
  cp *.a /usr/local/cuda/lib64/

# Install Folly dependencies
RUN apt-get update && apt-get install -y \
  g++ \
  automake \
  autoconf \
  autoconf-archive \
  libtool \
  libboost-all-dev \
  libevent-dev \
  libdouble-conversion-dev \
  libgoogle-glog-dev \
  libgflags-dev \
  liblz4-dev \
  liblzma-dev \
  libsnappy-dev \
  make \
  zlib1g-dev \
  binutils-dev \
  libjemalloc-dev \
  libssl-dev \
  libiberty-dev

# Clone Folly repo and move into it
RUN cd root && git clone https://github.com/facebook/folly.git && cd folly/folly && \
# Set up build
  autoreconf -ivf && \
  ./configure && \
# Make
  make && \
# Make install
  make install

# Install FBThrift dependencies
RUN apt-get install -y \
  flex \
  bison \
  libkrb5-dev \
  libsasl2-dev \
  libnuma-dev \
  pkg-config \
  libssl-dev

# Clone FBThrift repo and move into it
RUN cd root && git clone https://github.com/facebook/fbthrift.git && cd fbthrift/thrift && \
# Build
  ./deps.sh

# Clone TH++ repo and move into it
RUN cd root && git clone https://github.com/facebook/thpp.git && cd thpp/thpp && \
# Add Torch to CMake path
  export CMAKE_PREFIX_PATH=/root/torch/install && \
  export LD_LIBRARY_PATH=/root/folly/folly/.libs/:$LD_LIBRARY_PATH && \
# Incorporate fix (temporary)
  sed -i 's/--no-as-needed")/--no-as-needed,-lboost_thread,-lboost_system")/g' CMakeLists.txt && \
# Build
  ./build.sh

# Install fblualib dependencies
RUN apt-get install -y \
  libedit-dev \
  libmatio-dev \
  libpython-dev \
  python-numpy

# Clone fblualib repo and move into it
RUN cd root && git clone https://github.com/facebook/fblualib.git && cd fblualib/fblualib && \
# Build
  ./build.sh

# Clone nn repo and move into it
RUN cd root && git clone https://github.com/torch/nn && cd nn && \
# Checkout getParamsByDevice branch
  git checkout getParamsByDevice && \
# Make
  luarocks make rocks/nn-scm-1.rockspec

# Clone fbtorch repo and move into it
RUN cd root && git clone https://github.com/facebook/fbtorch.git && cd fbtorch && \
# Make
  luarocks make rocks/fbtorch-scm-1.rockspec

# Clone fbnn repo and move into it
RUN cd root && git clone https://github.com/facebook/fbnn.git && cd fbnn && \
# Make
  luarocks make rocks/fbnn-scm-1.rockspec

# Clone fbcunn repo and move into it
RUN cd root && git clone https://github.com/facebook/fbcunn.git && cd fbcunn && \
# Make
  luarocks make rocks/fbcunn-scm-1.rockspec

# Install autobw
luarocks install https://raw.githubusercontent.com/bshillingford/autobw.torch/master/autobw-scm-1.rockspec