FROM neurodebian:nd70
MAINTAINER Yaroslav Halchenko <yoh@neuro.debian.net>

USER root

# Speed up installation using our apt cacher
#RUN mkdir /etc/apt/apt.conf.d/

# CUSTOM -- remove copying of 99apt-cacher
COPY conf/etc/apt/apt.conf.d/99apt-cacher /etc/apt/apt.conf.d/99apt-cacher
RUN chmod a+r /etc/apt/apt.conf.d/99apt-cacher
# might interfer later with the testing, so -- disable
# ENV http_proxy http://172.17.42.1:3128

RUN sed -i -e 's,wheezy main$,wheezy-hcp500 main contrib non-free,g' /etc/apt/sources.list.d/neurodebian.sources.list
RUN sed -i -e 's, main$, main contrib non-free,g' /etc/apt/sources.list /etc/apt/sources.list.d/neurodebian.sources.list

# Assure popcon doesn't kick in
RUN bash -c "echo 'debconf debconf/frontend select noninteractive' | debconf-set-selections -"

RUN apt-get update && apt-get install -y connectome-workbench; \
    apt-get install -y fsl; \
    apt-get clean
# fsl-atlases?
# TODO RUN http_proxy=http://172.17.42.1:3128 wget FREESURFER # install the beast



# Setup optional beasts not available through APT
# CUSTOM -- remove line with http_proxy setting
RUN set -e; mkdir -p /opt; cd /opt;  \
    apt-get install -y git wget; \
    git clone --depth=1 https://github.com/Washington-University/Pipelines; \
    http_proxy=http://172.17.42.1:3128 \
      wget -q http://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/5.3.0-HCP/freesurfer-Linux-centos6_x86_64-stable-pub-v5.3.0-HCP.tar.gz; \
    tar -xzvf freesurfer-Linux-centos6_x86_64-stable-pub-v5.3.0-HCP.tar.gz; \
    rm -rf freesurfer-Linux-centos6_x86_64-stable-pub-v5.3.0-HCP.tar.gz;

RUN set -e; cd /opt;  \
    apt-get install -y python-dev python-nibabel python-scipy; \
    git clone git://github.com/Washington-University/gradunwarp && ( cd gradunwarp; python setup.py install; )

# additional tune ups of the environment

ENV FREESURFER_HOME /opt/freesurfer
ENV HCPPIPEDIR /opt/Pipelines
COPY conf/private/freesurfer_license.txt /opt/freesurfer/license.txt

RUN ln -sf bash /bin/sh; \
    echo "source /etc/fsl/fsl.sh;\nsource /opt/freesurfer/FreeSurferEnv.sh;" >> /root/.bashrc

# TODO: install gradient files from https://db.humanconnectome.org/app/action/ChooseDownloadResources?project=HCP_Resources&resource=SampleData&filePath=HcpPipelinesExampleDataNonGDC.zip
