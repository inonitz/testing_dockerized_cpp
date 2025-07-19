FROM alpine:latest
LABEL Description="Clang Based C++ Build environment"

ENV HOMEPATH='home'
ENV VSCodeClangdAbsPath='/usr/bin/clangd'
ENV VSCodeClangdQueryDriverAbsPath='/usr/bin/clang++'
ENV USER_NAME=cppguest
ENV GROUP_NAME=cppguest_group
ENV USER_ID=12345
ENV GROUP_ID=23456


# RUN addgroup -g $GROUP_ID $GROUP_NAME && \
#     adduser --shell /sbin/nologin --disabled-password \
#     --no-create-home --uid $USER_ID --ingroup $GROUP_NAME $USER_NAME


# Install Packages then switch users
USER root

# see: https://github.com/gliderlabs/docker-alpine/issues/24
# see: https://superuser.com/questions/1055060/how-to-install-a-specific-package-version-in-alpine
RUN apk update \
    && apk upgrade \
    && apk add --no-cache \
    make \
    cmake \
    ninja-build \
    git \
    wget \
    dpkg \
    clang \
    clang-dev \
    compiler-rt \
    lld \
    ccache \
    util-linux-dev \
    alpine-sdk \
    python3

RUN ln -sf /usr/bin/clang /usr/bin/cc \
    && ln -sf /usr/bin/clang++ /usr/bin/c++ \
    && update-alternatives --install /usr/bin/cc cc /usr/bin/clang 10\
    && update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang++ 10\
    && update-alternatives --auto cc \
    && update-alternatives --auto c++ \
    && update-alternatives --display cc \
    && update-alternatives --display c++ \
    && ls -l /usr/bin/cc /usr/bin/c++ \
    && cc --version \
    && c++ --version

# Build premake5 from source and add to /usr/bin
WORKDIR ${HOMEPATH}
RUN git clone -b master https://github.com/premake/premake-core && \
    cd ./premake-core && \
    ./Bootstrap.sh && \
    chmod a+rx bin/release/premake5 && \
    cp bin/release/premake5 /usr/local/bin/premake5 && \
    cd .. && \
    rm -rf ./premake-core


RUN git clone -b barebones https://github.com/inonitz/premake5-workspace-template && \
    cd premake5-workspace-template

WORKDIR ${HOMEPATH}/premake5-workspace-template


# # Now we finished setting up tools and we move to 'user space'
# USER guest

COPY entrypoint.sh /root/entrypoint.sh
ENTRYPOINT /root/entrypoint.sh