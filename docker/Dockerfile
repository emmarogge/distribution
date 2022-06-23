FROM ubuntu:20.04
ENV DEBIAN_FRONTEND=noninteractive

# Upgrade pip to avoid error:
#   Command "python setup.py egg_info" failed with error code 1
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
                wget \
                git
# Uncomment the below if you want to make a version with the GUI
# RUN apt install -y libwebkitgtk-3.0

# Set up environment
RUN export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
RUN export PATH=$PATH:/home/ubuntu/.local/bin

# # This is needed for a Python 3.8 quirk
RUN apt remove -y python3-pip
RUN wget https://bootstrap.pypa.io/get-pip.py
RUN python3.8 get-pip.py
RUN rm get-pip.py

#Install wx from a wheel
RUN wget https://extras.wxpython.org/wxPython4/extras/linux/gtk3/ubuntu-16.04/wxPython-4.1.0-cp38-cp38-linux_x86_64.whl
RUN python3.8 -m pip install wxPython-4.1.0-cp38-cp38-linux_x86_64.whl
RUN rm wxPython-4.1.0-cp38-cp38-linux_x86_64.whl

# RUN  apt-get -yq update && \
#      apt-get -yqq install ssh && \
#      apt-get install -y git

# Copy SSH credentials to access GitHub
RUN mkdir -p ~/.ssh && \
    chmod 0700 ~/.ssh && \
    ssh-keyscan github.com > ~/.ssh/known_hosts

# Do some linux magic to format the key properly.
RUN echo $github_private_key > ~/.ssh/original
RUN sed -i 's/ /\n/g' ~/.ssh/original
RUN sed -i '1,4d' ~/.ssh/original
RUN tac ~/.ssh/original | sed '1,4d' | tac > ~/.ssh/temp
RUN echo "-----BEGIN RSA PRIVATE KEY-----" > ~/.ssh/id_ed25519
RUN cat ~/.ssh/temp >> ~/.ssh/id_ed25519
RUN echo "-----END RSA PRIVATE KEY-----" >> ~/.ssh/id_ed25519
RUN chmod 600 ~/.ssh/id_ed25519

# Check GitHub SSH connection
RUN echo $(ssh -T git@github.com)

# Install CellProfiler
RUN python3.8 -m pip install numpy cython google-cloud-storage
# RUN python3.8 -m pip install git+https://github.com/emmarogge/core.git@issues/4547
# Clone CellProfiler-core module
RUN git clone https://github.com/emmarogge/core.git
# RUN ls -la
# RUN cd core/ && ls -la
# Build CellProfiler-core module from source at my PR.
# RUN python3.8 -m pip install .
RUN python3.8 -m pip install -e core/
# Clone CellProfiler project
RUN git clone https://github.com/emmarogge/CellProfiler.git
# Build from source (which will pull in my version of CellProfiler)
RUN python3.8 -m pip install -e CellProfiler


WORKDIR /usr/local/src
ENTRYPOINT ["cellprofiler"]
CMD ["--run", "--run-headless", "--help"]