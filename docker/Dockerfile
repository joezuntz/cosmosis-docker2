FROM condaforge/miniforge3:latest
MAINTAINER joezuntz@googlemail.com

ARG mpich=3.4.3
ARG mpich_prefix=mpich-$mpich


# Install everything through conda
RUN conda install -c conda-forge   -y cosmosis cosmosis-build-standard-library==1.0 mpich=${mpich}=external_* chrpath

# Except for MPI. We need that installed manually for shifter to be able to pick it up
RUN \
    wget https://www.mpich.org/static/downloads/${mpich}/${mpich_prefix}.tar.gz && \
    tar xvzf $mpich_prefix.tar.gz                                           && \
    cd $mpich_prefix                                                        && \
    # Something over-zealous in some versions of MPICH means that it breaks if you
    # have F90 set. This is patched in later versions so we can probably remove later.
    # We have to run all these inside conda run to get the right compilers etc
    conda run --no-capture-output bash -c "unset F90 && unset F90FLAGS && ./configure   --disable-wrapper-rpath --disable-cxx --with-device=ch3"  && \
    conda run --no-capture-output make                                      && \
    conda run --no-capture-output make install                              && \
    cd ..                                                                   && \
    rm -rf $mpich_prefix                                                    && \
    # This patches up the fact that when we installed MPI4PY the lib wasn't there
    dirname=$(python -c 'import mpi4py,os;print(os.path.split(mpi4py.__file__)[0])') && \
    chrpath -r /usr/local/lib/ ${dirname}/MPI.*.so


# Needed for mpi4py to pick up the replaced MPI library.
# Though actually that itself will be replaced by shifter. hmm
ENV LD_LIBRARY_PATH=/usr/local/lib

# Disable the local per-user python directory because
# it breaks things on NERSC
ENV PYTHONNOUSERSITE=1

# This directory should get mapped to the host
WORKDIR /opt/cosmosis

# Run everything under conda. This doesn't work under shifter but does
# under docker. So on NERSC we will provide some centrally-installed
# commands to do stuff instead.
ENTRYPOINT ["conda", "run", "--no-capture-output"]
