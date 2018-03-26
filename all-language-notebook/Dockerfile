# 修改自 https://github.com/cookeem/jupyter-all/blob/master/Dockerfile
FROM jupyter/minimal-notebook
ENV SUDOUSER=jupyter
ENV NB_USER=${SUDOUSER}  \
    HOME_DIR=/home/${SUDOUSER}
ENV JUPYTER_CONFIG_DIR=${HOME_DIR}/etc  \
    JUPYTER_RUNTIME_DIR=${HOME_DIR}/runtime \
    JUPYTER_DATA_DIR=${HOME_DIR}/.local/share/jupyter \
    IPYTHONDIR=${HOME_DIR}/ipython \
    NB_UID=1001 \
    NB_GID=1001 \
    HOME=${HOME_DIR}
    

USER root

RUN useradd -m -s/bin/bash ${SUDOUSER} && \
                echo ${SUDOUSER}:Hell0 | chpasswd && \
                echo "${SUDOUSER} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN set -x ; mkdir -p ${JUPYTER_RUNTIME_DIR} ${JUPYTER_DATA_DIR} ${IPYTHONDIR} ${JUPYTER_CONFIG_DIR} && \
    chown -R ${SUDOUSER} ${JUPYTER_RUNTIME_DIR} ${JUPYTER_DATA_DIR} ${IPYTHONDIR} ${JUPYTER_CONFIG_DIR} && \
    sed -i '/c.NotebookApp.open_browser/ac.NotebookApp.notebook_dir="./"' /etc/jupyter/jupyter_notebook_config.py && \
    cp /etc/jupyter/jupyter_notebook_config.py ${JUPYTER_CONFIG_DIR}


ENV JAVA_HOME=/usr/local/jdk1.8.0_151 \
    SBT_HOME=/usr/local/sbt \
    GOROOT=/usr/local/go \
    SCALA_HOME=/usr/local/scala-2.11.11

ENV GOPATH=${HOME_DIR}/gopath
ENV CLASSPATH=$JAVA_HOME/lib/tools.jar \
    PATH=$JAVA_HOME/bin:$SCALA_HOME/bin:$GOROOT/bin:$GOPATH/bin:$SBT_HOME/bin:$PATH

# root
# install nodejs and npm
RUN apt-get update && \
    apt-get install -y curl && \
    curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash - && \
    apt-get install -y nodejs && \
    apt-get install -y build-essential

# root
# install jdk
RUN wget http://dl.cookeem.com/jdk-8u151-linux-x64.tar.gz && \
tar zxvf jdk-8u151-linux-x64.tar.gz && \
mv jdk1.8.0_151 /usr/local/ && \
rm -rf jdk-8u151-linux-x64.tar.gz

# root
# install scala
RUN wget http://dl.cookeem.com/scala-2.11.11.tgz && \
tar zxvf scala-2.11.11.tgz && \
mv scala-2.11.11 /usr/local/ && \
rm -rf scala-2.11.11.tgz

# root
# install sbt
RUN wget http://dl.cookeem.com/sbt-1.0.2.tgz && \
tar zxvf sbt-1.0.2.tgz && \
mv sbt /usr/local/ && \
rm -rf sbt-1.0.2.tgz

# root
# install go
RUN wget http://dl.cookeem.com/go1.9.2.linux-amd64.tar.gz && \
tar zxvf go1.9.2.linux-amd64.tar.gz && \
mv go /usr/local/ && \
rm -rf go1.9.2.linux-amd64.tar.gz

# root
# install gophernotes dependency
RUN apt install -y pkg-config && \
apt-get install -y libzmq3-dev

# root
# install pip library
RUN cd ~ && \
mkdir -p ~/.pip && \
echo '[global]\n\
index-url = http://pypi.doubanio.com/simple/\n\
\n\
[install]\n\
trusted-host=pypi.doubanio.com'\
>> ~/.pip/pip.conf

RUN pip install jupyter_contrib_nbextensions && \
pip install scipy && pip install scikit-image && pip install bokeh && pip install pyecharts

RUN pip install --upgrade tensorflow -i http://pypi.doubanio.com/simple --trusted-host pypi.doubanio.com
RUN jupyter contrib nbextension install --system

# root
# install opencv and dlib
RUN pip install opencv-contrib-python && \
apt-get install -y build-essential cmake pkg-config && \
apt-get install -y libx11-dev libatlas-base-dev && \
apt-get install -y libgtk-3-dev libboost-python-dev && \
pip install dlib

USER ${SUDOUSER}

# jovyan
RUN cd ~ && \
mkdir -p ${HOME_DIR}/gopath && \
mkdir -p ${HOME_DIR}/work

# jovyan
# npm config set registry http://registry.npm.taobao.org/

# jovyan
# install jupyter-scala
# 会自动安装到 ~/.local/share/jupyter/kernels/ 下，不会引用 ${JUPYTER_DATA_DIR} 变量
RUN git clone https://github.com/jupyter-scala/jupyter-scala /tmp/jupyter-scala && \
cd /tmp/jupyter-scala && \
./jupyter-scala && \
cd .. && \
rm -rf /tmp/jupyter-scala && \
jupyter kernelspec list

# jovyan
# install gophernotes
RUN go get -u -v github.com/gopherdata/gophernotes && \
mkdir -p ~/.local/share/jupyter/kernels/gophernotes && \
cp $GOPATH/src/github.com/gopherdata/gophernotes/kernel/* ${JUPYTER_DATA_DIR}/kernels/gophernotes && \
jupyter kernelspec list

# jovyan
# install ijavascript
# 自动安装到 ${JUPYTER_DATA_DIR}/kernels 下
RUN sudo chown -R $SUDOUSER /opt/conda && npm install -g ijavascript && \
ijsinstall && \
jupyter kernelspec list

USER ${SUDOUSER}
ENV WDIR ${HOME}/work
RUN mkdir -p ${WDIR}
WORKDIR ${WDIR}