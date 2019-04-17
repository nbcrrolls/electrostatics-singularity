################################################################################# 
# Singularity Definition File
# 
# Version:          2.0
# Software:         Molecular electrostatics singularity image
# Software Version: 2019.4.16
# Description:      Docker image for BrownDye, APBS and PDB2PQR
# Website:          http://browndye.ucsd.edu
# Tags:             Electrostatics|Brownian Dynamics|Solvation
# Build command:    singularity build electrostatics.simg Singularity
# Pull command:     singularity pull shub://nbcrrolls/electrostatics-singularity
# Run command:      singularity shell nbcrrolls-electrostatics-singularity-master-latest.simg
# Run command:      singularity exec [image name] apbs input.in
# Run command:      singularity run --app apbs [image name] arguments
# Run command:      singularity run --app pdb2pqr [image name] arguments
#
################################################################################# 

Bootstrap: debootstrap
MirrorURL: http://us.archive.ubuntu.com/ubuntu
OSVersion: xenial # 16.04

#Bootstrap: docker
#From: ubuntu:16.04

%labels

    APPLICATION_NAME apbs-pdb2pqr-browndye
    APPLICATION_VERSION 1.5 + 2.1.0 + 2.0
    APPLICATION_URL http://www.poissonboltzmann.org + https://browndye.ucsd.edu

    SYSTEM_NAME comet
    SYSTEM_SINGULARITY_VERSION 2.6.1
    SYSTEM_URL http://www.sdsc.edu/support/user_guides/comet.html

    SINGULARITY_IMAGE_SIZE 1024

    ORIGINAL_AUTHOR_NAME Robert Konecny
    ORIGINAL_AUTHOR_EMAIL rok@ucsd.edu
    ORIGINAL_DEFINITION_FILE https://github.com/nbcrrolls/electrostatics-singularity/blob/master/Singularity

    MODIFYING_AUTHOR_NAME Marty Kandes
    MODIFYING_AUTHOR_EMAIL mkandes@sdsc.edu

    LAST_UPDATED 20190417

%help

    This is a singularity image with molecular electrostatics tools:
    apbs, pdb2pqr and BrownDye. All applications are intalled in /opt
    sub-directories and are in your $PATH.

%runscript
# this will get copied to /.singularity.d/runscript indide the container
# which will run whenever the container is called as an executable

#if [ $# -eq 0 ]
#then
  echo
  echo "========================================================================="
  echo "    This container includes the following apps:"
  echo
  echo "      APBS v1.5       https://www.poissonboltzmann.org"
  echo "      PDB2PQR v2.1.0  https://www.poissonboltzmann.org"
  echo "      BrownDye v2.0   https://browndye.ucsd.edu"
  echo
  echo " Please register your use of APBS and PDB2PQR at http://eepurl.com/by4eQr"
  echo
  echo " To list all available applications do 'singularity apps [image name]'"
  echo "========================================================================="
  echo
#else
  echo "Executing arguments: $*"
  exec "$@"
#fi

%post -c /bin/bash
    # Set system locale
    export LC_ALL=C
    set -o errexit
    APBS_VERSION=1.5
    PDB2PQR_VERSION=2.1.0
    APBS_URL=https://versaweb.dl.sourceforge.net/project/apbs/apbs/apbs-${APBS_VERSION}
    APBS_PKG=APBS-${APBS_VERSION}-linux64.tar.gz
    PDB2PQR_URL=https://versaweb.dl.sourceforge.net/project/pdb2pqr/pdb2pqr/pdb2pqr-${PDB2PQR_VERSION}
    PDB2PQR_PKG=pdb2pqr-linux-bin64-${PDB2PQR_VERSION}.tar.gz
    BD1_VERSION="1.0-13_Feb_2019"
    BD2_VERSION="2.0-11_Mar_2019"

    BD_URL=https://browndye.ucsd.edu
    BD1_PKG=browndye.tar.gz
    BD2_PKG=browndye2.tar.gz
    # needed for Ubuntu 18.04 (Bionic)
    #RL_URL=https://mirrors.edge.kernel.org/ubuntu/pool/main/r/readline6
    #RL_PKG=libreadline6_6.3-8ubuntu8_amd64.deb

    DEBIAN_FRONTEND=noninteractive
    apt-get -y install ubuntu-standard
    # Upgrade all packages to their latest versions
    apt-get -y update && apt-get -y upgrade
    apt-get -y install build-essential
    apt-get -y install curl
    #apt-get -y install make
    #apt-get -y install gcc
    #apt-get -y install g++
    #apt-get -y install ocaml
    apt-get -y install libexpat-dev
    apt-get -y install nano
    apt-get -y install readline-common
    apt-get -y install libgfortran3
    #apt-get -y install wget
    apt-get -y install libboost-dev
    apt-get -y install liblapack-dev
    # works with Ubuntu 16.04 (Xenial)
    apt-get -y install libreadline6
    cd /tmp

    wget http://caml.inria.fr/pub/distrib/ocaml-4.06/ocaml-4.06.0.tar.gz
    tar -xzvf ocaml-4.06.0.tar.gz
    cd ocaml-4.06.0
    ./configure
    make world.opt
    umask 022
    make install

    curl -k ${BD_URL}/${BD1_PKG} | tar xzf - -C /opt
    cd /opt/browndye
    curl -k -sO ${BD_URL}/browndye/doc/fixes.html
    make all

    curl -k ${BD_URL}/${BD2_PKG} | tar xzf - -C /opt
    cd /opt/browndye2
    curl -k -sO ${BD_URL}/browndye2/doc/fixes.html
    make all
    mkdir lib && cd lib
    curl -k -sO ${BD_URL}/coffdrop.xml.gz
    curl -k -sO ${BD_URL}/connectivity.xml
    curl -k -sO ${BD_URL}/map.xml
    curl -k -sO ${BD_URL}/charges.xml

    # needed for Ubuntu 18.04 (Bionic)
    #curl -k ${RL_URL}/${RL_PKG} -o /tmp/${RL_PKG}
    #dpkg -i /tmp/${RL_PKG}

    apt-get -y purge gcc
    apt-get -y purge g++
    #apt-get -y purge ocaml
    apt-get -y purge libexpat-dev
    apt-get -y purge libboost-dev
    apt-get -y clean
    apt-get -y autoremove
    apt-get -y install ca-certificates
    rm -rf /var/lib/apt/lists/*

    # add apbs
    wget -q --no-check-certificate ${APBS_URL}/${APBS_PKG} -O - | tar xzf - -C /opt
    ln -s /opt/APBS-${APBS_VERSION}-linux64 /opt/apbs

    # add pdb2pqr
    wget -q --no-check-certificate ${PDB2PQR_URL}/${PDB2PQR_PKG} -O - | tar xzf - -C /opt
    ln -s /opt/pdb2pqr-linux-bin64-${PDB2PQR_VERSION} /opt/pdb2pqr

    # Make filesystem mount points
    mkdir /cvmfs /oasis /projects /scratch

%environment

    # Set system locale
    export LC_ALL=C

    # Set APBS, PDB2PQR, and BrownDye environment variables
    export APBS_VERSION=1.5
    export APBS_PATH=/opt/apbs

    export PDB2PQR_VERSION=2.1.0
    export PDB2PQR_PATH=/opt/pdb2pqr

    export BD1_VERSION="1.0-13_Feb_2019"
    export BD1_PATH=/opt/browndye

    export BD2_VERSION="2.0-11_Mar_2019"
    export BD2_PATH=/opt/browndye2

    export PATH=${APBS_PATH}/bin:${PDB2PQR_PATH}:${BD2_PATH}/bin:$PATH
    export LD_LIBRARY_PATH=${APBS_PATH}/lib

%apphelp apbs

    apbs version 1.5
    For instructions on use see http://www.poissonboltzmann.org/
    apbs is installed in /opt/apbs
    
%apprun apbs

    /opt/apbs/bin/apbs "$@"

%apphelp pdb2pqr

    pdb2pqr version 2.1.0
    For instructions on use see http://www.poissonboltzmann.org/
    pdb2pqr is installed in /opt/pdb2pqr

%apprun pdb2pqr

    /opt/pdb2pqr/pdb2pqr "$@"

%apphelp nam_simulation

    BrownDye version 2.0
    For instructions on use see https://browndye.ucsd.edu
    All BrowDye programs are installed in /opt/browndye/bin

%apprun nam_simulation

    /opt/browndye2/bin/nam_simulation "$@"

%apphelp we_simulation

   BrownDye version 2.0
   For instructions on use see https://browndye.ucsd.edu

%apprun we_simulation

   /opt/browndye2/bin/we_simulation "$@"

#%apphelp pqr2xml
#
#    BrownDye version 2.0
#    For instructions on use see https://browndye.ucsd.edu
#
#%apprun pqr2xml
#
#    /opt/browndye2/bin/pqr2xml "$@"

#%apphelp make_rxn_pairs
#
#   BrownDye version 2.0
#   For instructions on use see https://browndye.ucsd.edu
#
#%apprun make_rxn_pairs
#
#   /opt/browndye2/bin/make_rxn_pairs "$@"

#%apphelp xyz_trajectory
#
#   BrownDye version 2.0
#   For instructions on use see https://browndye.ucsd.edu
#
#%apprun xyz_trajectory
#
#   /opt/browndye2/bin/xyz_trajectory "$@"
