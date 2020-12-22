# 2020-12-18

FROM ubuntu:18.04

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

# dependency packages
RUN apt-get update -q && \
    apt-get install -q -y \
        bzip2 \
        ca-certificates \
        git \
        libgl1-mesa-glx\
        libglib2.0-0 \
        libsm6 \
        libxext6 \
        libxrender1 \
        mercurial \
        subversion \
        wget \
        autoconf automake libtool \
        pkg-config \
        libpng-dev \
        libjpeg8-dev \
        libtiff5-dev \
        zlib1g-dev \ 
        libicu-dev \
        libpango1.0-dev \
        libcairo2-dev \
        libleptonica-dev \
    && apt-get clean


# --- miniconda section ---
ENV PATH /opt/conda/bin:$PATH

CMD [ "/bin/bash" ]

# Leave these args here to better use the Docker build cache
ARG CONDA_VERSION=py38_4.9.2
ARG CONDA_MD5=122c8c9beb51e124ab32a0fa6426c656

RUN wget --no-check-certificate --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O miniconda.sh && \
    echo "${CONDA_MD5}  miniconda.sh" > miniconda.md5 && \
    if ! md5sum --status -c miniconda.md5; then exit 1; fi && \
    mkdir -p /opt && \
    sh miniconda.sh -b -p /opt/conda && \
    rm miniconda.sh miniconda.md5 && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy
# --- miniconda section ---


# setting custom args
ARG init_dir=image_init_src
RUN mkdir ${init_dir}
ADD ${init_dir} /${init_dir}
RUN chmod 777 -R ${init_dir}

# ------------------- tesseract install section -------------------
# install tsx dependencies
RUN apt-get update -q && apt-get install -q -y \
	bzip2 \
	git \
	wget \
	libgl1-mesa-glx\
	libglib2.0-0 \
	libsm6 \
	libxext6 \
	libxrender1 \
	mercurial \
	subversion \
	autoconf automake libtool \
	pkg-config \
	libpng-dev \
	libjpeg8-dev \
	libtiff5-dev \
	zlib1g-dev \ 
	libicu-dev \
	libpango1.0-dev \
	libcairo2-dev \
	libleptonica-dev \
&& apt-get clean

ARG tsx_src_dir=${init_dir}/tesseract-4.0.0

# core installation
RUN cd ${tsx_src_dir} && \
	./autogen.sh && \
    ./configure && \
    make && \
    make install && \
    ldconfig

# add langs data
RUN wget --no-check-certificate https://github.com/tesseract-ocr/tessdata_best/raw/master/eng.traineddata && \
    mv eng.traineddata /usr/local/share/tessdata/ && \
    wget --no-check-certificate https://github.com/tesseract-ocr/tessdata_best/raw/master/chi_tra.traineddata && \
    mv chi_tra.traineddata /usr/local/share/tessdata/
# ------------------- tesseract install section -------------------

# --- python env section ---
WORKDIR /Cathay3
ADD . /Cathay3
ARG CONDA_VENV=dev1

RUN . /opt/conda/etc/profile.d/conda.sh && \
    conda create -y --name ${CONDA_VENV} python=3.7 && \
    conda activate ${CONDA_VENV} && \
    cd ${init_dir} && \
    pip install -r requirements.txt
# --- python env section ---

RUN ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime