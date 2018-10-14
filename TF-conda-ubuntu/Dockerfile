#Build env for Tensorflowv1.12 using MKL and Anaconda Python 3.7

FROM ubuntu:16.04
ENV PATH /opt/conda/bin:$PATH
#ENV http_proxy http://proxy-us.intel.com:911
#ENV https_proxy http://proxy-us.intel.com:912
ARG TINI_VERSION=0.18.0
ARG ANANCONDA="Anaconda3-5.3.0-Linux-x86_64.sh"
RUN \
  apt-get update && apt-get install -y \
  curl \
  vim \
  git \
  bzip2 \
  grep \
  sed \
  dpkg \
  ca-certificates \
  libglib2.0-0 libxext6 libsm6 libxrender1 \
  && rm -rf /var/lib/lists/*

# install anaconda
RUN curl -L "https://repo.anaconda.com/archive/Anaconda3-5.3.0-Linux-x86_64.sh" > ~/anaconda.sh && \
  /bin/bash ~/anaconda.sh -b -p /opt/conda && \
  rm ~/anaconda.sh && \
  ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
  echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
  echo "conda activate base" >> ~/.bashrc

# install an init env
RUN curl -L "https://github.com/krallin/tini/releases/download/v0.18.0/tini_0.18.0-amd64.deb" > tini.deb && \ 
	dpkg -i tini.deb

# create a tensorflow env and install tensorflow cpu
  RUN \
  conda create -n tflow_env tensorflow

ENTRYPOINT [ "/usr/bin/tini", "--"]
CMD [ "/bin/bash" ]

