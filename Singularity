################################################################################# 
# Singularity conf file
# 
# Version:          2.0
# Software:         Molecular electrostatics singularity image
# Software Version: 2019-3.6
# Description:      Docker image for BrownDye, APBS and PDB2PQR
# Website:          http://browndye.ucsd.edu
# Tags:             Electrostatics|Brownian Dynamics|Solvation
# Build command:    singularity build electrostatics.sif Singularity
# Run command:      singularity shell electrostatics.sif
# Run command:      singularity run --app apbs electrostatics.sif
# Run command:      singularity run --app pdb2pqr electrostatics.sif
################################################################################# 

Bootstrap: docker
From: ubuntu:18.04

%labels
   Maintainer Robert Konecny <rok@ucsd.edu>
   Description Docker image for BrownDye, APBS and PDB2PQR
   Version v2.0

%help
This is a singularity image with molecular electrostatics tools: apbs, pdb2pqr and BrownDye
All applications are intalled in /opt sub-directories and are in your $PATH.

%runscript
cat << EOF

This container includes the following apps:
APBS v1.5 - http://www.poissonboltzmann.org/
PDB2PQR v2.1.0 - http://www.poissonboltzmann.org/
BrownDye v2.0 (13 Feb 2019) - https://browndye.ucsd.edu/

Please register your use of APBS and PDB2PQR at http://eepurl.com/by4eQr

To list all available applications do "singularity apps electrostatics.sif"

EOF

%post 
set -o errexit
APBS_VERSION=1.5
PDB2PQR_VERSION=2.1.0
APBS_URL=https://versaweb.dl.sourceforge.net/project/apbs/apbs/apbs-${APBS_VERSION}
APBS_PKG=APBS-${APBS_VERSION}-linux64.tar.gz
PDB2PQR_URL=https://versaweb.dl.sourceforge.net/project/pdb2pqr/pdb2pqr/pdb2pqr-${PDB2PQR_VERSION}
PDB2PQR_PKG=pdb2pqr-linux-bin64-${PDB2PQR_VERSION}.tar.gz
BD_URL=https://browndye.ucsd.edu
BD_PKG=browndye.tar.gz
RL_URL=https://mirrors.edge.kernel.org/ubuntu/pool/main/r/readline6
RL_PKG=libreadline6_6.3-8ubuntu8_amd64.deb

DEBIAN_FRONTEND=noninteractive
apt-get update
apt-get -y install --no-install-recommends curl make gcc g++ ocaml \
        libexpat-dev nano readline-common libgfortran3 wget

curl -k ${BD_URL}/${BD_PKG} | tar xzf - -C /opt
cd /opt/browndye
curl -k -sO ${BD_URL}/browndye/doc/fixes.html
make all

curl -k ${RL_URL}/${RL_PKG} -o /tmp/${RL_PKG}
dpkg -i /tmp/${RL_PKG}
apt-get -y purge gcc g++ ocaml libexpat-dev
apt-get -y clean
apt-get -y autoremove
apt-get -y install ca-certificates
rm -rf /var/lib/apt/lists/*
#/tmp/* /var/tmp/*

# add apbs
wget --no-check-certificate ${APBS_URL}/${APBS_PKG} -O - | tar xzf - -C /opt
ln -s /opt/APBS-${APBS_VERSION}-linux64 /opt/apbs

# add pdb2pqr
wget --no-check-certificate ${PDB2PQR_URL}/${PDB2PQR_PKG} -O - | tar xzf - -C /opt
ln -s /opt/pdb2pqr-linux-bin64-${PDB2PQR_VERSION} /opt/pdb2pqr

%environment
BD_PATH=/opt/browndye
APBS_PATH=/opt/apbs
PDB2PQR_PATH=/opt/pdb2pqr

PATH=${APBS_PATH}/bin:${PDB2PQR_PATH}:${BD_PATH}/bin:$PATH
LD_LIBRARY_PATH=${APBS_PATH}/lib

export BD_PATH APBS_PATH PDB2PQR_PATH LD_LIBRARY_PATH PATH

. /singularity

%apphelp apbs
apbs version 1.5
For instructions on use see http://www.poissonboltzmann.org/
apbs is installed in /opt/apbs
    
%apprun apbs
/opt/apbs/bin/apbs

%apphelp pdb2pqr
pdb2pqr version 2.1.0
For instructions on use see http://www.poissonboltzmann.org/
pdb2pqr is installed in /opt/pdb2pqr

%apprun pdb2pqr
/opt/pdb2pqr/pdb2pqr

%apphelp nam_simulation
BrownDye version 2.0 (13 Feb 2019)
For instructions on use see https://browndye.ucsd.edu
All BrowDye programs are installed in /opt/browndye/bin

%apprun nam_simulation
/opt/browndye/bin/nam_simulation

%apphelp pqr2xml
BrownDye version 2.0 (13 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun pqr2xml
/opt/browndye/bin/pqr2xml

%apphelp make_rxn_pairs
BrownDye version 2.0 (13 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun make_rxn_pairs
/opt/browndye/bin/make_rxn_pairs

%apphelp we_simulation
BrownDye version 2.0 (13 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun we_simulation
/opt/browndye/bin/we_simulation

%apphelp xyz_trajectory
BrownDye version 2.0 (13 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun xyz_trajectory
/opt/browndye/bin/xyz_trajectory
