FROM ubuntu:20.04
ENV DEBIAN_FRONTEND=noninteractive
# ENV PATH="/usr/local/bin:${PATH}"
# ENV PYTHONPATH="/usr/local/lib/python3.8:/usr/local/bin"

RUN apt-get -y update && \
    apt-get -y upgrade && \
    apt-get -y install -y software-properties-common 
RUN add-apt-repository ppa:deadsnakes/ppa
RUN apt -y update
RUN apt -y upgrade

# Install CellProfiler dependencies
RUN apt-get -y install \
                git \
                libmysqlclient-dev \
                libnotify-dev \
                libsdl2-dev \
                openjdk-8-jdk-headless \
                python3-pip \
                python3.8-dev \
                python3.8-distutils \
                ssh \
                wget

# Set up environment
RUN export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
RUN export PATH=$PATH:/home/ubuntu/.local/bin

# This is needed for a Python 3.8 quirk
RUN apt remove -y python3-pip
RUN wget https://bootstrap.pypa.io/get-pip.py
RUN python3.8 get-pip.py
RUN rm get-pip.py

#Install wx from a wheel
RUN wget https://extras.wxpython.org/wxPython4/extras/linux/gtk3/ubuntu-16.04/wxPython-4.1.0-cp38-cp38-linux_x86_64.whl
# RUN python3.8 -m pip install wxPython-4.1.0-cp38-cp38-linux_x86_64.whl --src=/usr/local/bin
RUN python3.8 -m pip install wxPython-4.1.0-cp38-cp38-linux_x86_64.whl
RUN rm wxPython-4.1.0-cp38-cp38-linux_x86_64.whl

# Check GitHub SSH connection
# RUN echo $(ssh -T git@github.com)
RUN python3.8 -m pip install numpy cython google-cloud-storage

# Clone CellProfiler-core module
# RUN python3.8 -m pip install --user git+https://github.com/emmarogge/core.git@issues/4547#egg=cellprofiler-core

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
RUN mkdir -p -m 0600 ~/.ssh && ssh-keyscan github.com >> ~/.ssh/known_hosts

# Build from source (which will pull in my version of CellProfiler)
RUN --mount=type=ssh python3.8 -m pip install git+https://github.com/emmarogge/CellProfiler.git@pr-dependency#egg=cellprofiler --src=/home

WORKDIR /usr/local/src

ENTRYPOINT ["cellprofiler"]
CMD ["--run", "--run-headless", "--help"]