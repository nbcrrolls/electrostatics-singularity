################################################################################# 
# Singularity conf file
# 
# Version:          2.0
# Software:         Molecular electrostatics singularity image
# Software Version: 2019.3.7
# Description:      Docker image for BrownDye, APBS and PDB2PQR
# Website:          http://browndye.ucsd.edu
# Tags:             Electrostatics|Brownian Dynamics|Solvation
# Build command:    singularity build electrostatics.simg Singularity
# Pull command:     singularity pull shub://nbcrrolls/electrostatics-singularity
# Run command:      singularity shell nbcrrolls-electrostatics-singularity-master-latest.simg
# Run command:      singularity exec [image name] apbs input.in
# Run command:      singularity run --app apbs [image name] arguments
# Run command:      singularity run --app pdb2pqr [image name] arguments
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
BrownDye v2.0 (26 Feb 2019) - https://browndye.ucsd.edu/

Please register your use of APBS and PDB2PQR at http://eepurl.com/by4eQr

To list all available applications do "singularity apps [image name]"

EOF

%post 
set -o errexit
APBS_VERSION=1.5
PDB2PQR_VERSION=2.1.0
APBS_URL=https://versaweb.dl.sourceforge.net/project/apbs/apbs/apbs-${APBS_VERSION}
APBS_PKG=APBS-${APBS_VERSION}-linux64.tar.gz
PDB2PQR_URL=https://versaweb.dl.sourceforge.net/project/pdb2pqr/pdb2pqr/pdb2pqr-${PDB2PQR_VERSION}
PDB2PQR_PKG=pdb2pqr-linux-bin64-${PDB2PQR_VERSION}.tar.gz
BD1_VERSION="1.0-13-Feb_2019"
BD2_VERSION="2.0-26_Feb_ 2019"                                                                                                    
BD_URL=https://browndye.ucsd.edu
BD1_PKG=browndye.tar.gz
BD2_PKG=browndye2.tar.gz
RL_URL=https://mirrors.edge.kernel.org/ubuntu/pool/main/r/readline6
RL_PKG=libreadline6_6.3-8ubuntu8_amd64.deb

DEBIAN_FRONTEND=noninteractive
apt-get update
apt-get -y install --no-install-recommends curl make gcc g++ ocaml \
        libexpat-dev nano readline-common libgfortran3 wget libboost-dev liblapack-dev

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

curl -k ${RL_URL}/${RL_PKG} -o /tmp/${RL_PKG}
dpkg -i /tmp/${RL_PKG}
apt-get -y purge gcc g++ ocaml libexpat-dev libboost-dev
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

%environment
BD1_PATH=/opt/browndye
BD2_PATH=/opt/browndye2
APBS_PATH=/opt/apbs
PDB2PQR_PATH=/opt/pdb2pqr
APBS_VERSION=1.5
PDB2PQR_VERSION=2.1.0
BD1_VERSION="1.0-13-Feb_2019"
BD2_VERSION="2.0-26_Feb_ 2019"                                                                                                    

PATH=${APBS_PATH}/bin:${PDB2PQR_PATH}:${BD2_PATH}/bin:$PATH
LD_LIBRARY_PATH=${APBS_PATH}/lib

export APBS_VERSION PDB2PQR_VERSION BD1_VERSION BD2_VERSION
export BD1_PATH BD2_PATH APBS_PATH PDB2PQR_PATH LD_LIBRARY_PATH PATH

. /singularity

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
BrownDye version 2.0 (26 Feb 2019)
For instructions on use see https://browndye.ucsd.edu
All BrowDye programs are installed in /opt/browndye/bin

%apprun nam_simulation
/opt/browndye2/bin/nam_simulation "$@"

%apphelp pqr2xml
BrownDye version 2.0 (26 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun pqr2xml
/opt/browndye2/bin/pqr2xml "$@"

%apphelp make_rxn_pairs
BrownDye version 2.0 (26 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun make_rxn_pairs
/opt/browndye2/bin/make_rxn_pairs "$@"

%apphelp we_simulation
BrownDye version 2.0 (26 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun we_simulation
/opt/browndye2/bin/we_simulation "$@"

%apphelp xyz_trajectory
BrownDye version 2.0 (26 Feb 2019)
For instructions on use see https://browndye.ucsd.edu

%apprun xyz_trajectory
/opt/browndye2/bin/xyz_trajectory "$@"
