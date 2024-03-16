FROM ubuntu:22.04 AS builder
ARG TAG

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
    && apt-get upgrade -y -o Dpkg::Options::="--force-confold" \
    && apt-get install --no-install-recommends --yes \
        build-essential \
        cmake \
        git \
        python3 \
        ca-certificates \
        ninja-build \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN git clone --depth 1 --depth=100 "https://github.com/llvm/llvm-project.git" llvm-${TAG}
RUN cd llvm-${TAG} && git reset --hard ${TAG}
RUN cd llvm-${TAG} && \
    cmake -S llvm -B build -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DLLVM_ENABLE_PROJECTS=clang \
        -DLLVM_ENABLE_RUNTIMES="libcxx;libcxxabi;libunwind" \
        -DCMAKE_INSTALL_PREFIX=./install
RUN cd llvm-${TAG} && ninja -C build clang
RUN cd llvm-${TAG} && ninja -C build cxx cxxabi unwind
RUN cd llvm-${TAG} && ninja -C build install-cxx install-cxxabi install-unwind
RUN cd llvm-${TAG} && ninja -C build install

FROM debian:stable-slim AS clang
ARG TAG

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        openssl \
        ca-certificates \
        git \
        cmake \
        make \
        binutils \
        gcc \
        libc6-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
COPY --from=builder /llvm-${TAG}/install /llvm-${TAG}/

ENV C_COMPILER=/llvm-${TAG}/bin/clang
ENV CXX_COMPILER=/llvm-${TAG}/bin/clang++
ENV LD_LIBRARY_PATH=/llvm-${TAG}/lib/x86_64-unknown-linux-gnu/
ENV CXXFLAGS="${CXXFLAGS} -stdlib=libc++"