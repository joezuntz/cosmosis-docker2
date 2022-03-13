FROM condaforge/miniforge3:latest
MAINTAINER joezuntz@googlemail.com

# Install everything through conda
RUN conda install -c conda-forge   -y cosmosis cosmosis-build-standard-library==1.0 mpich

# Disable the local per-user python directory because
# it breaks things on NERSC
ENV PYTHONNOUSERSITE=1

# This directory should get mapped to the host
WORKDIR /opt/cosmosis

# Run everything under conda
ENTRYPOINT ["conda", "run", "--no-capture-output"]