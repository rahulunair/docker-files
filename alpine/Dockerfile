#Clear essentials

FROM alpine:edge
ENV JAVA_HOME /usr/lib/jvm/java-1.8-openjdk
ENV TENSORFLOW_VERSION 1.11.0
ENV LOCAL_RESOURCES 8912,4,1.0

ADD https://raw.githubusercontent.com/davido/bazel-alpine-package/master/david@ostrovsky.org-5a0369d6.rsa.pub \
    /etc/apk/keys/david@ostrovsky.org-5a0369d6.rsa.pub
ADD https://github.com/davido/bazel-alpine-package/releases/download/0.15.2/bazel-0.15.2-r0.apk \
    /tmp/bazel-0.15.2-r0.apk
RUN apk add /tmp/bazel-0.15.2-r0.apk
#ENV http_proxy http://proxy-us.intel.com:911
#ENV https_proxy http://proxy-us.intel.com:912
ARG tf_cpu="https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-1.11.0-cp36-cp36m-linux_x86_64.whl"

RUN echo "https://nl.alpinelinux.org/alpine/edge/community" >> \
          /etc/apk/repositories
RUN echo "https://nl.alpinelinux.org/alpine/edge/testing" >> \	
          /etc/apk/repositories
RUN  apk update && \
     apk add dumb-init \
           musl musl-dev libc6-compat linux-headers \
	   build-base bash git ca-certificates \ 
           strace \
           vim \
	   python3 \
	   hdf5 hdf5-dev \
	   alpine-sdk \
	   wget \
	   gcc \
	   py-numpy-dev \
	   make cmake g++ sed patch \
	   py3-pip py3-numpy py3-numpy-f2py \
	   openblas-dev sed swig zip bash 

RUN ln -s -f /usr/bin/python3 /usr/bin/python2
RUN ln -s /usr/bin/pip3 /usr/bin/pip
RUN pip install --upgrade pip
RUN pip install ipython


RUN cd /tmp \
    && curl -SL https://github.com/tensorflow/tensorflow/archive/v${TENSORFLOW_VERSION}.tar.gz \
        | tar xzf -

# Build Tensorflow
RUN cd /tmp/tensorflow-${TENSORFLOW_VERSION} \
    && : musl-libc does not have "secure_getenv" function \
    && sed -i -e '/JEMALLOC_HAVE_SECURE_GETENV/d' third_party/jemalloc.BUILD \
    && sed -i -e '/define TF_GENERATE_BACKTRACE/d' tensorflow/core/platform/default/stacktrace.h \
    && sed -i -e '/define TF_GENERATE_STACKTRACE/d' tensorflow/core/platform/stacktrace_handler.cc \
    && PYTHON_BIN_PATH=/usr/bin/python3 CC_OPT_FLAGS="-march=native" \
        TF_NEED_JEMALLOC=1 \
        TF_NEED_GCP=0 \
        TF_NEED_HDFS=0 \
        TF_NEED_S3=0 \
        TF_ENABLE_XLA=0 \
        TF_NEED_GDR=0 \
        TF_NEED_VERBS=0 \
        TF_NEED_OPENCL=0 \
        TF_NEED_CUDA=0 \
        TF_NEED_MPI=0 \
        bash configure
RUN cd /tmp/tensorflow-${TENSORFLOW_VERSION} \
    && bazel build -c opt --local_resources ${LOCAL_RESOURCES} \
    //tensorflow/tools/pip_package:build_pip_package
RUN cd /tmp/tensorflow-${TENSORFLOW_VERSION} \
    && ./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
RUN cp /tmp/tensorflow_pkg/tensorflow-${TENSORFLOW_VERSION}-cp36-cp36m-linux_x86_64.whl /root

# Make sure it's built properly
RUN pip3 install --no-cache-dir /root/tensorflow-${TENSORFLOW_VERSION}-cp36-cp36m-linux_x86_64.whl

CMD [ "/bin/bash" ]

