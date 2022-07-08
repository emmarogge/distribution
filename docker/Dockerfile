FROM ubuntu:20.04

RUN apt-get update \
  && apt-get install -y python3-pip python3-dev wget unzip \
  && cd /usr/local/bin \
  && ln -s /usr/bin/python3 python \
  && pip3 install --upgrade pip


RUN pip install --upgrade pip

# ARG version=4.2.0

ARG DEBIAN_FRONTEND=noninteractive

# Install CellProfiler dependencies
RUN apt-get --assume-yes install -y software-properties-common

RUN add-apt-repository ppa:deadsnakes/ppa
RUN apt -y update
RUN apt -y upgrade
# RUN apt install -y python3.8-dev python3.8-distutils python3-pip
RUN apt-get install -y openjdk-11-jdk
# RUN openjdk-8-jdk-headless
RUN apt-get install -y libmysqlclient-dev libnotify-dev libsdl2-dev wget git
# Uncomment the below if you want to make a version with the GUI
# RUN apt install -y libwebkitgtk-3.0

# Set up environment
ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
ENV PATH=$PATH:/home/ubuntu/.local/bin

#Install wx from a wheel
RUN wget https://extras.wxpython.org/wxPython4/extras/linux/gtk3/ubuntu-16.04/wxPython-4.1.0-cp38-cp38-linux_x86_64.whl
# RUN python3.8 -m pip install wxPython-4.1.0-cp38-cp38-linux_x86_64.whl
RUN pip install wxPython-4.1.0-cp38-cp38-linux_x86_64.whl
RUN rm wxPython-4.1.0-cp38-cp38-linux_x86_64.whl

# Check GitHub SSH connection
# RUN echo $(ssh -T git@github.com)
RUN pip install numpy cython

# Clone CellProfiler-core module
RUN git clone -b issues/4547 --single-branch https://github.com/emmarogge/core.git@issues/4547
RUN git clone -b issues/4547 --single-branch https://github.com/emmarogge/CellProfiler.git@pr-dependency#egg=cellprofiler
# Clone CellProfiler project
# RUN python3.8 -m pip install --user git+https://github.com/emmarogge/CellProfiler.git@pr-dependency#egg=cellprofiler
# Download public key for github.com
# RUN mkdir -p ~/.ssh && \
#     chmod 0700 ~/.ssh

# This is necessary to prevent the "git clone" operation from failing
# with an "unknown host key" error.
# RUN mkdir -m 700 /root/.ssh; \
#   touch -m 600 /root/.ssh/known_hosts; \
#   ssh-keyscan github.com > /root/.ssh/known_hosts
# RUN mkdir -p -m 0600 ~/.ssh && ssh-keyscan github.com >> ~/.ssh/known_hosts

# Build from source (which will pull in my version of CellProfiler)
# RUN --mount=type=ssh python3.8 -m pip install git+https://github.com/emmarogge/CellProfiler.git@pr-dependency#egg=cellprofiler

WORKDIR core
RUN pip install .

WORKDIR ../CellProfiler
RUN pip install .

WORKDIR /usr/local/src
ENTRYPOINT ["cellprofiler"]

CMD ["--run", "--run-headless", "--help"]