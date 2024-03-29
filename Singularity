Bootstrap: docker
From: ubuntu
%post

LANG=C.UTF-8
LC_ALL=C.UTF-8

# Uncomment below to have the docker use a proxy.
# Change the URL to refer to your preferred proxy.
#ENV http_proxy http://my.proxy:8080
#ENV https_proxy http://my.proxy:8080
#ENV HTTP_PROXY http://my.proxy:8080
#ENV HTTPS_PROXY http://my.proxy:8080
#RUN echo 'Acquire::http::proxy \"http://my.proxy:8080/\";' >> /etc/apt/apt.conf.d/00proxy
#RUN echo 'Acquire::https::proxy \"http://my.proxy:8080/\";' >> /etc/apt/apt.conf.d/00proxy

# Install some prerequisites
apt-get update && \
apt-get upgrade -y && \
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends --fix-missing \
g++ \
gfortran \
git \
patch \
python-dev \
python-pip \
python-virtualenv \
liblapack-dev \
libopenblas-dev \
unzip \
virtualenv \
wget \
&& \
apt-get clean && \
apt-get autoremove && \
rm -rf /var/lib/apt/lists/*

# Set up the python virtualenv: theano, lasagne and pylearn2
virtualenv /mlpy
. mlpy/bin/activate && \
pip install --upgrade pip && \
pip install git+https://github.com/Theano/Theano.git@rel-0.9.0beta1 && \
pip install https://github.com/Lasagne/Lasagne/archive/master.zip && \
pip install Pillow && \
pip install bitstring && \
pip install numpy==1.11.0 && \
mkdir -p mlpy/src && \
cd mlpy/src && \
git clone https://github.com/lisa-lab/pylearn2 && \
cd pylearn2 && \
python setup.py develop && \
rm -rf /.cache/pip

# Set .bashrc for virtualenv PYLEARN2, THEANO
# Change number of threads to match your environment
echo "source /mlpy/bin/activate" >> /.bashrc && \
echo "export PYLEARN2_DATA_PATH=/.pylearn2" >> /.bashrc && \
echo "export OMP_NUM_THREADS=4" >> /.bashrc

# Set the theano configuration file
echo "[global]" >> /.theanorc && \
echo "floatX = float32" >> /.theanorc && \
echo "device = gpu" >> /.theanorc && \
echo "openmp = True" >> /.theanorc && \
echo "openmp_elemwise_minsize = 200000" >> /.theanorc && \
echo "" >> /.theanorc && \
echo "[nvcc]" >> /.theanorc && \
echo "fastmath = True" >> /.theanorc && \
echo "" >> /.theanorc && \
echo "[blas]" >> /.theanorc && \
echo "ldflags = -lopenblas" >> /.theanorc

# Make script to download datasets 
echo '#!/bin/bash' >> /download_cifar10_mnist.sh && \
echo 'cd /mlpy/src/pylearn2/pylearn2/scripts/datasets' >> /download_cifar10_mnist.sh && \
echo 'python download_mnist.py' >> /download_cifar10_mnist.sh && \
echo './download_cifar10.sh' >> /download_cifar10_mnist.sh && \
chmod 755 /download_cifar10_mnist.sh

%environment
export LANG=C.UTF-8
export LC_ALL=C.UTF-8
%runscript
exec /bin/bash /bin/bash "$@"
%startscript
exec /bin/bash /bin/bash "$@"