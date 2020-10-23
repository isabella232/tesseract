FROM ubuntu:focal

# General dependencies
RUN apt-get -qq update
RUN apt-get install -qq -y llvm-10 clang clang-10 libclang-10-dev cmake wget curl zip
RUN apt-get install -qq -y git

# Install lsif-clang
ENV LSIF_CLANG_REV=366659086a6ba2968c837179c17ff978cf3efc97
RUN git clone https://github.com/sourcegraph/lsif-clang.git
WORKDIR /lsif-clang
RUN git checkout $LSIF_CLANG_REV
RUN cmake -B build
RUN make -C build install
WORKDIR /

# src-cli
ENV SRC_ACCESS_TOKEN=""
ENV SRC_ENDPOINT="https://sourcegraph.com"
RUN curl -L https://sourcegraph.com/.api/src-cli/src_linux_amd64 -o /usr/local/bin/src
RUN chmod +x /usr/local/bin/src

# Install Bear
RUN apt-get install -y bear

#
# Build Tesseract
#
ENV PROJECT_REV=""
RUN git clone --depth=10 https://github.com/tesseract-ocr/tesseract.git
WORKDIR /tesseract
RUN git checkout $PROJECT_REV
# Deps
RUN apt-get install -y autoconf automake libtool
RUN apt-get install -y pkg-config
RUN apt-get install -y libpng-dev
RUN apt-get install -y libjpeg8-dev
RUN apt-get install -y libtiff5-dev
RUN apt-get install -y zlib1g-dev
RUN apt-get install -y libleptonica-dev
RUN apt-get install -y pkg-config
# Generate dump.lsif
RUN mkdir -p /install
RUN ./autogen.sh
RUN ./configure --prefix=/install
RUN bear make
RUN lsif-clang compile_commands.json

# Upload
ENV SRC_ACCESS_TOKEN=""
ENV SRC_ENDPOINT="https://sourcegraph.com"
ENTRYPOINT src lsif upload
