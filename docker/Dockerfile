FROM condaforge/miniforge3:latest
MAINTAINER joezuntz@googlemail.com


# Make, which we will need for everything
RUN apt-get update -y \
    && DEBIAN_FRONTEND="noninteractive" apt-get install -y make  \
    && apt-get clean all

# We need a C compiler temporarily to install MPICH, which we want so that we use
# vendored MPI with conda. Not sure if we can get away with removing the gcc and g++
# compilers afterwards to save space but will try. We will use the conda gcc for everything
# else, though ideally everything would come through conda-forge.
# I have found that using the conda-forge supplied MPICH does not work.
RUN apt-get update -y  \
    && DEBIAN_FRONTEND="noninteractive" apt-get install -y gcc \
    && mkdir /opt/mpich \
    && cd /opt/mpich \
    && wget http://www.mpich.org/static/downloads/3.4.2/mpich-3.4.2.tar.gz \
    && tar xvzf mpich-3.4.2.tar.gz \
    && cd mpich-3.4.2 \
    && ./configure --disable-wrapper-rpath --disable-fortran --disable-cxx --with-device=ch3 && make \
    && make install \
    && rm -rf /opt/mpich \
    && apt-get remove --purge -y gcc


# Install everything through conda
RUN conda install -c conda-forge   -y cosmosis cosmosis-build-standard-library==1.0 'mpich>=3.4.*=external_*' chrpath

ENV LD_LIBRARY_PATH /usr/local/lib

# Disable the local per-user python directory because
# it breaks things on NERSC
ENV PYTHONNOUSERSITE=1

# This directory should get mapped to the host
WORKDIR /opt/cosmosis

# Run everything under conda. This doesn't work under shifter but does
# under docker. So on NERSC we will provide some centrally-installed
# commands to do stuff instead.
ENTRYPOINT ["conda", "run", "--no-capture-output"]
