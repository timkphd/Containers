Bootstrap: docker
From: ubuntu:16.04

%post
    apt-get -y update
    apt-get -y install wget
    apt-get -y install apt-transport-https
# Next lines require to download Intel Python
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    apt-key add GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    wget https://apt.repos.intel.com/setup/intelproducts.list -O /etc/apt/sources.list.d/intelproducts.list
    apt-get -y update
# This prevents some questions being asked
    export DEBIAN_FRONTEND=noninteractive
    apt-get install dialog
date
# Intalling Fortran also give us C.    
    apt install -y gfortran
# Installing g++ make installing openmpi easier
    apt install -y g++
    apt install -y make
date

# Install editors
  apt install -y nano
  apt install -y vim

# lpr might be needed enscript
  apt install -y lpr

# The next two lines install a perl module needed 
# by the backup app installed below.
    apt install -y cpanminus
    cpan install Term::ANSIColor

# Install Rrrrrrrr
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E298A3A825C0D65DFD57CBB651716619E084DAB9
apt-get -y install software-properties-common
add-apt-repository 'deb https://cloud.r-project.org/bin/linux/ubuntu xenial-cran35/'
apt update
apt install -y r-base


######  Let's see what happens if we install mpi ######
# Intel MPI is huge
apt-get -y install intel-mpi-2019.3-062 2019.3-062


# Build openmpi from source - should not look for IB fabric
# This will, at best be slow over network and most likely
# will only work for a single node.
# We need ssh for this to work.

apt install -y openssh-server
apt install -y openssh-client

mkdir -p /opt/install
cd /opt/install
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.1.tar.gz
tar -xzf openmpi-4.0.1.tar.gz
cd openmpi-4.0.1
./configure

make
make install

export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
export PATH=/usr/local/bin:$PATH


# Install Rmpi
mkdir -p /opt/install/rlibs
cd /opt/install
wget https://cran.r-project.org/src/contrib/Rmpi_0.6-9.tar.gz
R CMD INSTALL -l /opt/install/rlibs --configure-args=" \
--with-Rmpi-include='/usr/local/include' \
--with-Rmpi-libpath='/usr/local/lib' \
--with-mpi='/usr/local' \
--with-Rmpi-type='OPENMPI' " \
Rmpi_0.6-9.tar.gz 




date
    apt-get -y install intelpython3
date
    #apt-get -y install intelpython2
date
#
#
# Build a cute calendar app.
# This is a combination of a Fortran and Python program
# The python program creates the input file, calls the
# Fortran program and then post processes the output by
# calling enscript, built below.
# envoke as:
###### mycal
## or
###### mycal 1 2019 1 2020
# creates a "snoopy" calendar as a *ps file
    mkdir -p /opt/examples
    chmod 755 /opt/examples
    wget   https://petra.acns.colostate.edu/docs/examples/snoopy/cal.f -O /opt/examples/cal.f
    wget   https://petra.acns.colostate.edu/docs/examples/snoopy/cal.py -O /opt/examples/mycal
    gfortran /opt/examples/cal.f -o /opt/examples/snoopy
    chmod 755 /opt/examples/*
#
#### apt install -y enscript
# build enscript manually to get a newest version
    cd /opt/examples
    wget https://ftp.gnu.org/gnu/enscript/enscript-1.6.6.tar.gz
    tar -xzf enscript-1.6.6.tar.gz 
    cd enscript-1.6.6/
    ./configure
    make
    make install
    cd /opt/examples
    rm -rf enscript*

# Add C and Python matxix inversion examples
    cd /opt/examples
    wget https://petra.acns.colostate.edu/docs/examples/array/invertc.c
    gcc -fopenmp invertc.c -o invertc
    wget https://petra.acns.colostate.edu/docs/examples/array/invertp.py
    mv invertp.py invertp
    chmod 755 invertp
# fix the python version to point to the default installed version
    sed -i '1 s,^.*$,#!/usr/bin/env python,' invertp
  
# Get a few other tools
    wget https://petra.acns.colostate.edu/docs/examples/tims_tools/backup
    chmod 755 backup
    for t in tymer confidence.py plplot greenbar jlines ; do 
      wget https://petra.acns.colostate.edu/docs/examples/tims_tools/$t
      # fix the python version to point to the default installed version
      sed -i '1 s,^.*$,#!/usr/bin/env python,' $t
      chmod 755 $t
    done


# Get the rest of Tim's examples:
    mkdir -p /opt/examples/tims
    cd /opt/examples/tims
    wget https://petra.acns.colostate.edu/docs/examples/examples.tgz
    tar -xzf examples.tgz
    rm examples.tgz

# Do a bit of cleanup.  This directory is ~300MB
    rm -rf /opt/install/openmpi-4.0.1*
    rm -rf /opt/examples/tims/*tgz

date    
%environment
    export LC_ALL=C
# Python order is important if you want both 3 and 2  
# because there is "python" under intelpython3 that is
# actually python3
    export PATH=/opt/intel/intelpython3/bin:$PATH
   #export PATH=/opt/intel/intelpython2/bin:$PATH
    export PATH=/opt/install:$PATH
    export PATH=/opt/examples:$PATH
    export PATH=/usr/local/bin:$PATH
    export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
    export export R_LIBS=/opt/install/rlibs/
    export DO_IMPI=/opt/intel/impi/2019.3.199/intel64/bin/mpivars.sh

%labels
    Author thkphd
#done
