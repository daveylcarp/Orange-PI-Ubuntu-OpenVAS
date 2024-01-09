get Ubuntu from  https://github.com/Joshua-Riek/ubuntu-rockchip#readme
Click on the Latest Release
Currently trying 
https://github.com/Joshua-Riek/ubuntu-rockchip/releases/download/v1.32/ubuntu-22.04.3-preinstalled-server-arm64-orangepi-5b.img.xz
Server - defaults are
ubuntu
ubuntu

It will force a password change on login
I suggest changing the username as well

```sh
sudo usermod -l newname -d /home/newname -m ubuntu
```

and change the user's group name.

```sh
groupmod -n newname ubuntu
```


setting up wifi.

```sh
ls /sys/class/net
```

Look for the name of your interface. This should start with a w. mine was wlan0

```sh
ls /etc/netplan

sudo nano 50-cloud-init.yaml
```

make that file look similar to this. make sure that the wifi's lines up with either the 'ethernets:' or the 'version:' lines

```bash
network:
    ethernets:
        eth0:
            dhcp4: true
            optional: true
    version: 2
    wifis:
        wlan0:
            optional: true
            access-points:
                "SSID-NAME-HERE":
                    password: "PASSWORD-HERE"
            dhcp4: true
```

ctrl-x to exit, yes save it, and enter to keep the file name.

```sh
sudo netplan --debug apply
```

check for the ipaddress and make note of what it is for later.
```sh
ifconfig
```

Now to add remote ssh login, if you would like it.

```sh
sudo apt update && sudo apt upgrade
```

```sh
sudo apt install openssh-server
```

```sh
service ssh status
```

```sh
sudo systemctl status ssh
```

```sh
sudo systemctl enable --now ssh
```
```sh
sudo systemctl start ssh
```
```sh
sudo ufw allow ssh
```
## Prerequisites[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#prerequisites "Link to this heading")

> [!NOTE]
> 
> Please follow the guide step by step. Later steps might require settings or output of a previous command. 

> [!Important]
> THESE ONLY HAVE THE INSTRUCTIONS TO UBUNTU 22.04!!!!

The command `sudo` is used for executing commands that require privileged access on the system.

### Creating a User and a Group[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#creating-a-user-and-a-group "Link to this heading")

The services provided by the Greenbone Community Edition should run as a dedicated user and group. Therefore a `gvm` user and a group with the same name will be created.


Creating a gvm system user and group[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id1 "Link to this code")

```sh
sudo useradd -r -M -U -G sudo -s /usr/sbin/nologin gvm
```

### Adjusting the Current User[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#adjusting-the-current-user "Link to this heading")

To allow the current user to run _gvmd_ he must be added to the _gvm_ group. To make the group change effective either logout and login again or use **su**.

Add current user to gvm group[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id3 "Link to this code")

```sh
sudo usermod -aG gvm $USER

su $USER
```

### Choosing an Install Prefix[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#choosing-an-install-prefix "Link to this heading")

Before building the software stack, a (root) directory must be chosen where the built software will finally be installed. For example, when building packages, the distribution developers set this path to `/usr`.

By default, it is `/usr/local` which is also used in this guide. This directory will be stored in an environment variable `INSTALL_PREFIX` to be able to reference it later.

Setting an install prefix environment variable[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id4 "Link to this code")

```sh
export INSTALL_PREFIX=/usr/local
```
### Setting the PATH[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-the-path "Link to this heading")

On Debian systems the locations `/sbin`, `/usr/sbin` and `/usr/local/sbin` are not in the `PATH` of normal users. To run _gvmd_ which is located in `/usr/local/sbin` the `PATH` environment variable should be adjusted.

Adjusting PATH for running gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id5 "Link to this code")

```sh
export PATH=$PATH:$INSTALL_PREFIX/sbin
```
### Creating a Source, Build and Install Directory[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#creating-a-source-build-and-install-directory "Link to this heading")

To separate the sources and the build artifacts, a source and a build directory must be created.

This source directory will be used later in this guide via an environment variable `SOURCE_DIR`. Accordingly, a variable `BUILD_DIR` will be set for the build directory. Both can be set to any directory to which the current user has write permissions. Therefore directories in the current user’s home directory are chosen in this guide.

Choosing a source directory[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id6 "Link to this code")

```sh
export SOURCE_DIR=$HOME/source
mkdir -p $SOURCE_DIR
```

Choosing a build directory[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id7 "Link to this code")

```sh
export BUILD_DIR=$HOME/build
mkdir -p $BUILD_DIR
```

Additionally, an install directory will be set as an environment variable `INSTALL_DIR`. It is used as a temporary installation directory before moving all built artifacts to the final destination.

Choosing a temporary install directory[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id8 "Link to this code")

```sh
export INSTALL_DIR=$HOME/install
mkdir -p $INSTALL_DIR
```

### Choosing the Installation Source[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#choosing-the-installation-source "Link to this heading")

For building the GVM software stack, three different sources can be chosen depending on the desired stability:

- Building from release [tarballs](https://en.wikipedia.org/wiki/Tar_(computing))
    
- Building from git tags
    
- Building from release branches
    

Linux distributions use the release [tarballs](https://en.wikipedia.org/wiki/Tar_(computing)) because it is the most common and well known method to share source code.

Newer build systems may stick with the git tags.

If you are a developer and very familiar with building from source already, you may also try out using the git release branches. These have the advantage that they contain the newest fixes which may not yet be included in the release tarballs or git tags. As a downside, the release branches may contain only partially fixed issues and need to be updated more often.

This guide will use the tarballs to build the software.

### Installing Common Build Dependencies[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#installing-common-build-dependencies "Link to this heading")

For downloading, configuring, building and installing the Greenbone Community Edition components, several tools and applications are required. To install this requirements the following commands can be used:


Installing common build dependencies[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id9 "Link to this code")

```sh
sudo apt update
sudo apt install --no-install-recommends --assume-yes \
  build-essential \
  curl \
  cmake \
  pkg-config \
  python3 \
  python3-pip \
  gnupg
```

### Importing the Greenbone Signing Key[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#importing-the-greenbone-signing-key "Link to this heading")

To validate the integrity of the downloaded source files, [GnuPG](https://www.gnu.org/) is used. It requires downloading the Greenbone Community Signing public key and importing it into the current user’s keychain.

Importing the Greenbone Community Signing key[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id12 "Link to this code")

```sh
curl -f -L https://www.greenbone.net/GBCommunitySigningKey.asc -o /tmp/GBCommunitySigningKey.asc
gpg --import /tmp/GBCommunitySigningKey.asc
```

For understanding the validation output of the gpg tool, it is best to mark the Greenbone Community Signing key as fully trusted.

Setting the trust level for the Greenbone Community Signing key[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id13 "Link to this code")

```sh
echo "8AE4BE429B60A59B311C2E739823FAA60ED1E580:6:" | gpg --import-ownertrust
```
## Building and Installing the Components[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#building-and-installing-the-components "Link to this heading")

> [!NOTE]
>  
> The components should be build and installed in the listed order.

### gvm-libs[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#gvm-libs "Link to this heading")

_gvm-libs_ is a C library providing basic functionality like XML parsing and network communication. It is used in _openvas-scanner_, _gvmd_, _gsad_ and _pg-gvm_.

Setting the gvm-libs version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id14 "Link to this code")
```sh
export GVM_LIBS_VERSION=22.7.3
```

Required dependencies for gvm-libs[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id15 "Link to this code")
```sh
sudo apt install -y \
  libglib2.0-dev \
  libgpgme-dev \
  libgnutls28-dev \
  uuid-dev \
  libssh-gcrypt-dev \
  libhiredis-dev \
  libxml2-dev \
  libpcap-dev \
  libnet1-dev \
  libpaho-mqtt-dev
```

Optional dependencies for gvm-libs[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id16 "Link to this code")

```sh
sudo apt install -y \
  libldap2-dev \
  libradcli-dev
```

Downloading the gvm-libs sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id21 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/gvm-libs/archive/refs/tags/v$GVM_LIBS_VERSION.tar.gz -o $SOURCE_DIR/gvm-libs-$GVM_LIBS_VERSION.tar.gz
curl -f -L https://github.com/greenbone/gvm-libs/releases/download/v$GVM_LIBS_VERSION/gvm-libs-v$GVM_LIBS_VERSION.tar.gz.asc -o $SOURCE_DIR/gvm-libs-$GVM_LIBS_VERSION.tar.gz.asc
```

Verifying the source file[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id22 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/gvm-libs-$GVM_LIBS_VERSION.tar.gz.asc $SOURCE_DIR/gvm-libs-$GVM_LIBS_VERSION.tar.gz
```

The output of the last command should be similar to:

> [!NOTE]
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]

If the signature is valid, the tarball can be extracted.

```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/gvm-libs-$GVM_LIBS_VERSION.tar.gz
```

Afterwards, gvm-libs can be build and installed.


Building gvm-libs[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id23 "Link to this code")

```sh
mkdir -p $BUILD_DIR/gvm-libs && cd $BUILD_DIR/gvm-libs

cmake $SOURCE_DIR/gvm-libs-$GVM_LIBS_VERSION \
  -DCMAKE_INSTALL_PREFIX=$INSTALL_PREFIX \
  -DCMAKE_BUILD_TYPE=Release \
  -DSYSCONFDIR=/etc \
  -DLOCALSTATEDIR=/var

make -j$(nproc)
```

Installing gvm-libs[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id25 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/gvm-libs

make DESTDIR=$INSTALL_DIR/gvm-libs install

sudo cp -rv $INSTALL_DIR/gvm-libs/* /
```

### gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#gvmd "Link to this heading")

The Greenbone Vulnerability Management Daemon (gvmd) is the main service of the Greenbone Community Edition. It handles authentication, scan management, vulnerability information, reporting, alerting, scheduling and much more. As a storage backend, it uses a PostgreSQL database.

Setting the gvmd version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id26 "Link to this code")

```sh
export GVMD_VERSION=23.0.1
```


Required dependencies for gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id27 "Link to this code")

```sh
sudo apt install -y \
  libglib2.0-dev \
  libgnutls28-dev \
  libpq-dev \
  postgresql-server-dev-14 \
  libical-dev \
  xsltproc \
  rsync \
  libbsd-dev \
  libgpgme-dev
```

Optional dependencies for gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id28 "Link to this code")

```sh
sudo apt install -y --no-install-recommends \
  texlive-latex-extra \
  texlive-fonts-recommended \
  xmlstarlet \
  zip \
  rpm \
  fakeroot \
  dpkg \
  nsis \
  gnupg \
  gpgsm \
  wget \
  sshpass \
  openssh-client \
  socat \
  snmp \
  python3 \
  smbclient \
  python3-lxml \
  gnutls-bin \
  xml-twig-tools
```

Details about the optional dependencies can be found at [https://github.com/greenbone/gvmd/blob/stable/INSTALL.md#prerequisites-for-optional-features](https://github.com/greenbone/gvmd/blob/stable/INSTALL.md#prerequisites-for-optional-features)

Downloading the gvmd sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id35 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/gvmd/archive/refs/tags/v$GVMD_VERSION.tar.gz -o $SOURCE_DIR/gvmd-$GVMD_VERSION.tar.gz
curl -f -L https://github.com/greenbone/gvmd/releases/download/v$GVMD_VERSION/gvmd-$GVMD_VERSION.tar.gz.asc -o $SOURCE_DIR/gvmd-$GVMD_VERSION.tar.gz.asc
```

Verifying the source file[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id36 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/gvmd-$GVMD_VERSION.tar.gz.asc $SOURCE_DIR/gvmd-$GVMD_VERSION.tar.gz
```

The output of the last command should be similar to:


> [!NOTE]
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]


If the signature is valid the tarball can be extracted.


```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/gvmd-$GVMD_VERSION.tar.gz
```


Building gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id37 "Link to this code")

```sh
mkdir -p $BUILD_DIR/gvmd && cd $BUILD_DIR/gvmd

cmake $SOURCE_DIR/gvmd-$GVMD_VERSION \
  -DCMAKE_INSTALL_PREFIX=$INSTALL_PREFIX \
  -DCMAKE_BUILD_TYPE=Release \
  -DLOCALSTATEDIR=/var \
  -DSYSCONFDIR=/etc \
  -DGVM_DATA_DIR=/var \
  -DGVMD_RUN_DIR=/run/gvmd \
  -DOPENVAS_DEFAULT_SOCKET=/run/ospd/ospd-openvas.sock \
  -DGVM_FEED_LOCK_PATH=/var/lib/gvm/feed-update.lock \
  -DSYSTEMD_SERVICE_DIR=/lib/systemd/system \
  -DLOGROTATE_DIR=/etc/logrotate.d

make -j$(nproc)
```


Installing gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id39 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/gvmd

make DESTDIR=$INSTALL_DIR/gvmd install

sudo cp -rv $INSTALL_DIR/gvmd/* /
```

### pg-gvm[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#pg-gvm "Link to this heading")

_pg-gvm_ is a PostgreSQL server extension that adds several functions used by _gvmd_, e.g., iCalendar and host range evaluation. In previous versions, these functions were managed directly by _gvmd_ while _pg-gvm_ uses the extension management built into PostgreSQL.

Setting the pg-gvm version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id41 "Link to this code")

```sh
export PG_GVM_VERSION=22.6.1
```


Required dependencies for pg-gvm[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id42 "Link to this code")

```sh
sudo apt install -y \
  libglib2.0-dev \
  postgresql-server-dev-14 \
  libical-dev
```

Downloading the pg-gvm sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id45 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/pg-gvm/archive/refs/tags/v$PG_GVM_VERSION.tar.gz -o $SOURCE_DIR/pg-gvm-$PG_GVM_VERSION.tar.gz
curl -f -L https://github.com/greenbone/pg-gvm/releases/download/v$PG_GVM_VERSION/pg-gvm-$PG_GVM_VERSION.tar.gz.asc -o $SOURCE_DIR/pg-gvm-$PG_GVM_VERSION.tar.gz.asc
```

Verifying the source file[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id46 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/pg-gvm-$PG_GVM_VERSION.tar.gz.asc $SOURCE_DIR/pg-gvm-$PG_GVM_VERSION.tar.gz
```

The output of the last command should be similar to:


> [!NOTE]
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]

If the signature is valid the tarball can be extracted.

```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/pg-gvm-$PG_GVM_VERSION.tar.gz
```

Afterwards, pg-gvm can be build and installed.


Building pg-gvm[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id47 "Link to this code")

```sh
mkdir -p $BUILD_DIR/pg-gvm && cd $BUILD_DIR/pg-gvm

cmake $SOURCE_DIR/pg-gvm-$PG_GVM_VERSION \
  -DCMAKE_BUILD_TYPE=Release

make -j$(nproc)
```

Installing pg-gvm[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id49 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/pg-gvm

make DESTDIR=$INSTALL_DIR/pg-gvm install

sudo cp -rv $INSTALL_DIR/pg-gvm/* /
```

### Greenbone Security Assistant[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#greenbone-security-assistant "Link to this heading")

The Greenbone Security Assistant (GSA) sources consist of two parts:

- Web server _gsad_
    
- Web application _GSA_
    

#### GSA[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#gsa "Link to this heading")

The web application is written in JavaScript and relies on the [react framework](https://reactjs.org/). It uses [nodejs](https://nodejs.org/) for building the application and [yarn](https://classic.yarnpkg.com/lang/en/) for maintaining the JavaScript dependencies. Because the installation of yarn and the specific nodejs version requires a setup of external package repositories and the build process takes a lot of time, pre-built distributable files are available. These pre-built distributable files are used in this docs.

Setting the GSA version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id50 "Link to this code")

```sh
export GSA_VERSION=22.8.0
```

Downloading the gsa sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id51 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/gsa/releases/download/v$GSA_VERSION/gsa-dist-$GSA_VERSION.tar.gz -o $SOURCE_DIR/gsa-$GSA_VERSION.tar.gz
curl -f -L https://github.com/greenbone/gsa/releases/download/v$GSA_VERSION/gsa-dist-$GSA_VERSION.tar.gz.asc -o $SOURCE_DIR/gsa-$GSA_VERSION.tar.gz.asc
```

Verifying the source files[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id52 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/gsa-$GSA_VERSION.tar.gz.asc $SOURCE_DIR/gsa-$GSA_VERSION.tar.gz
```

The output of both commands should be similar to:


> [!NOTE]
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]


If the signatures are valid, the two tarballs can be extracted.

```sh
mkdir -p $SOURCE_DIR/gsa-$GSA_VERSION
tar -C $SOURCE_DIR/gsa-$GSA_VERSION -xvzf $SOURCE_DIR/gsa-$GSA_VERSION.tar.gz
```

Installing gsa[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id53 "Link to this code")

```sh
sudo mkdir -p $INSTALL_PREFIX/share/gvm/gsad/web/
sudo cp -rv $SOURCE_DIR/gsa-$GSA_VERSION/* $INSTALL_PREFIX/share/gvm/gsad/web/
```

#### gsad[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#gsad "Link to this heading")

The web server _gsad_ is written in the C programming language. It serves static content like images and provides an API for the web application. Internally it communicates with _gvmd_ using [GMP](https://greenbone.github.io/docs/latest/glossary.html#term-GMP).

Setting the GSAd version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id54 "Link to this code")

```sh
export GSAD_VERSION=22.7.0
```


Required dependencies for gsad[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id55 "Link to this code")

```sh
sudo apt install -y \
  libmicrohttpd-dev \
  libxml2-dev \
  libglib2.0-dev \
  libgnutls28-dev
```

Downloading the gsad sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id57 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/gsad/archive/refs/tags/v$GSAD_VERSION.tar.gz -o $SOURCE_DIR/gsad-$GSAD_VERSION.tar.gz
curl -f -L https://github.com/greenbone/gsad/releases/download/v$GSAD_VERSION/gsad-$GSAD_VERSION.tar.gz.asc -o $SOURCE_DIR/gsad-$GSAD_VERSION.tar.gz.asc
```
Verifying the source files[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id58 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/gsad-$GSAD_VERSION.tar.gz.asc $SOURCE_DIR/gsad-$GSAD_VERSION.tar.gz
```

The output of both commands should be similar to:


> [!NOTE]
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]


If the signatures are valid, the two tarballs can be extracted.

```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/gsad-$GSAD_VERSION.tar.gz
```

Building gsad[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id59 "Link to this code")

```sh
mkdir -p $BUILD_DIR/gsad && cd $BUILD_DIR/gsad

cmake $SOURCE_DIR/gsad-$GSAD_VERSION \
  -DCMAKE_INSTALL_PREFIX=$INSTALL_PREFIX \
  -DCMAKE_BUILD_TYPE=Release \
  -DSYSCONFDIR=/etc \
  -DLOCALSTATEDIR=/var \
  -DGVMD_RUN_DIR=/run/gvmd \
  -DGSAD_RUN_DIR=/run/gsad \
  -DLOGROTATE_DIR=/etc/logrotate.d

make -j$(nproc)
```

Installing gsad[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id60 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/gsad

make DESTDIR=$INSTALL_DIR/gsad install

sudo cp -rv $INSTALL_DIR/gsad/* /
```

### openvas-smb[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#openvas-smb "Link to this heading")

_openvas-smb_ is a helper module for _openvas-scanner_. It includes libraries (openvas-wmiclient/openvas-wincmd) to interface with Microsoft Windows Systems through the Windows Management Instrumentation API and a winexe binary to execute processes remotely on that system.

It is an optional dependency of _openvas-scanner_ but is required for scanning Windows-based systems.

> [!Warning]
> 
> _openvas-smb_ doesn’t work on CentOS at the moment! It is not a hard requirement.

Setting the openvas-smb version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id61 "Link to this code")

```sh
export OPENVAS_SMB_VERSION=22.5.3
```


Required dependencies for openvas-smb[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id62 "Link to this code")

```sh
sudo apt install -y \
  gcc-mingw-w64 \
  libgnutls28-dev \
  libglib2.0-dev \
  libpopt-dev \
  libunistring-dev \
  heimdal-dev \
  perl-base

```
Downloading the openvas-smb sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id64 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/openvas-smb/archive/refs/tags/v$OPENVAS_SMB_VERSION.tar.gz -o $SOURCE_DIR/openvas-smb-$OPENVAS_SMB_VERSION.tar.gz
curl -f -L https://github.com/greenbone/openvas-smb/releases/download/v$OPENVAS_SMB_VERSION/openvas-smb-v$OPENVAS_SMB_VERSION.tar.gz.asc -o $SOURCE_DIR/openvas-smb-$OPENVAS_SMB_VERSION.tar.gz.asc
```

Verifying the source file[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id65 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/openvas-smb-$OPENVAS_SMB_VERSION.tar.gz.asc $SOURCE_DIR/openvas-smb-$OPENVAS_SMB_VERSION.tar.gz
```

The output of the last command should be similar to:

> [!NOTE]
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]

If the signature is valid, the tarball can be extracted.

```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/openvas-smb-$OPENVAS_SMB_VERSION.tar.gz
```

Building openvas-smb[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id66 "Link to this code")

```sh
mkdir -p $BUILD_DIR/openvas-smb && cd $BUILD_DIR/openvas-smb

cmake $SOURCE_DIR/openvas-smb-$OPENVAS_SMB_VERSION \
  -DCMAKE_INSTALL_PREFIX=$INSTALL_PREFIX \
  -DCMAKE_BUILD_TYPE=Release

make -j$(nproc)
```
Installing openvas-smb[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id67 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/openvas-smb

make DESTDIR=$INSTALL_DIR/openvas-smb install

sudo cp -rv $INSTALL_DIR/openvas-smb/* /
```

### openvas-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#openvas-scanner "Link to this heading")

_openvas-scanner_ is a full-featured scan engine that executes a continuously updated and extended feed of [Vulnerability Tests (VTs)](https://greenbone.github.io/docs/latest/glossary.html#term-VT). The feed consist of thousands of NASL (Network Attack Scripting Language) scripts which implement all kind of vulnerability checks.

Setting the openvas-scanner version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id68 "Link to this code")

```sh
export OPENVAS_SCANNER_VERSION=22.7.6
```


Required dependencies for openvas-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id69 "Link to this code")

```sh
sudo apt install -y \
  bison \
  libglib2.0-dev \
  libgnutls28-dev \
  libgcrypt20-dev \
  libpcap-dev \
  libgpgme-dev \
  libksba-dev \
  rsync \
  nmap \
  libjson-glib-dev \
  libbsd-dev
```

Debian optional dependencies for openvas-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id70 "Link to this code")

```sh
sudo apt install -y \
  python3-impacket \
  libsnmp-dev
```

Downloading the openvas-scanner sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id74 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/openvas-scanner/archive/refs/tags/v$OPENVAS_SCANNER_VERSION.tar.gz -o $SOURCE_DIR/openvas-scanner-$OPENVAS_SCANNER_VERSION.tar.gz
curl -f -L https://github.com/greenbone/openvas-scanner/releases/download/v$OPENVAS_SCANNER_VERSION/openvas-scanner-v$OPENVAS_SCANNER_VERSION.tar.gz.asc -o $SOURCE_DIR/openvas-scanner-$OPENVAS_SCANNER_VERSION.tar.gz.asc
```

Verifying the source file[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id75 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/openvas-scanner-$OPENVAS_SCANNER_VERSION.tar.gz.asc $SOURCE_DIR/openvas-scanner-$OPENVAS_SCANNER_VERSION.tar.gz

```
The output of the last command should be similar to:

> [!NOTE]
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]

If the signature is valid, the tarball can be extracted.

```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/openvas-scanner-$OPENVAS_SCANNER_VERSION.tar.gz
```

Building openvas-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id76 "Link to this code")

```sh
mkdir -p $BUILD_DIR/openvas-scanner && cd $BUILD_DIR/openvas-scanner

cmake $SOURCE_DIR/openvas-scanner-$OPENVAS_SCANNER_VERSION \
  -DCMAKE_INSTALL_PREFIX=$INSTALL_PREFIX \
  -DCMAKE_BUILD_TYPE=Release \
  -DINSTALL_OLD_SYNC_SCRIPT=OFF \
  -DSYSCONFDIR=/etc \
  -DLOCALSTATEDIR=/var \
  -DOPENVAS_FEED_LOCK_PATH=/var/lib/openvas/feed-update.lock \
  -DOPENVAS_RUN_DIR=/run/ospd

make -j$(nproc)
```

Installing openvas-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id77 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/openvas-scanner

make DESTDIR=$INSTALL_DIR/openvas-scanner install

sudo cp -rv $INSTALL_DIR/openvas-scanner/* /
```

### ospd-openvas[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#ospd-openvas "Link to this heading")

_ospd-openvas_ is an [OSP server](https://greenbone.github.io/docs/latest/glossary.html#term-OSP) implementation to allow [gvmd](https://greenbone.github.io/docs/latest/glossary.html#term-gvmd) to remotely control an openvas-scanner. It is running as a daemon and waits for incoming OSP requests from _gvmd_.

Setting the ospd and ospd-openvas versions to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id78 "Link to this code")

```sh
export OSPD_OPENVAS_VERSION=22.6.1
```


Required dependencies for ospd-openvas[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id79 "Link to this code")

```sh
sudo apt install -y \
  python3 \
  python3-pip \
  python3-setuptools \
  python3-packaging \
  python3-wrapt \
  python3-cffi \
  python3-psutil \
  python3-lxml \
  python3-defusedxml \
  python3-paramiko \
  python3-redis \
  python3-gnupg \
  python3-paho-mqtt
```
Downloading the ospd-openvas sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id82 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/ospd-openvas/archive/refs/tags/v$OSPD_OPENVAS_VERSION.tar.gz -o $SOURCE_DIR/ospd-openvas-$OSPD_OPENVAS_VERSION.tar.gz
curl -f -L https://github.com/greenbone/ospd-openvas/releases/download/v$OSPD_OPENVAS_VERSION/ospd-openvas-v$OSPD_OPENVAS_VERSION.tar.gz.asc -o $SOURCE_DIR/ospd-openvas-$OSPD_OPENVAS_VERSION.tar.gz.asc
```

Verifying the source files[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id83 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/ospd-openvas-$OSPD_OPENVAS_VERSION.tar.gz.asc $SOURCE_DIR/ospd-openvas-$OSPD_OPENVAS_VERSION.tar.gz
```

> [!NOTE]
> The output of the last command should be similar to:
> 
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]

If the signatures are valid, the tarballs can be extracted.

```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/ospd-openvas-$OSPD_OPENVAS_VERSION.tar.gz
```


Installing ospd-openvas[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id84 "Link to this code")

```sh
cd $SOURCE_DIR/ospd-openvas-$OSPD_OPENVAS_VERSION

mkdir -p $INSTALL_DIR/ospd-openvas

python3 -m pip install --root=$INSTALL_DIR/ospd-openvas --no-warn-script-location .

sudo cp -rv $INSTALL_DIR/ospd-openvas/* /
```

### notus-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#notus-scanner "Link to this heading")

_notus-scanner_ is used for detecting vulnerable products by evaluating internal system information gathered by _openvas-scanner_. It communicates with _openvas-scanner_ and _ospd-openvas_ via [MQTT](https://en.wikipedia.org/wiki/MQTT). It is running as a daemon.

Setting the notus version to use[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id86 "Link to this code")

```sh
export NOTUS_VERSION=22.6.0
```


Required dependencies for notus-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id87 "Link to this code")

```sh
sudo apt install -y \
  python3 \
  python3-pip \
  python3-setuptools \
  python3-paho-mqtt \
  python3-psutil \
  python3-gnupg
```

Downloading the notus-scanner sources[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id90 "Link to this code")

```sh
curl -f -L https://github.com/greenbone/notus-scanner/archive/refs/tags/v$NOTUS_VERSION.tar.gz -o $SOURCE_DIR/notus-scanner-$NOTUS_VERSION.tar.gz
curl -f -L https://github.com/greenbone/notus-scanner/releases/download/v$NOTUS_VERSION/notus-scanner-v$NOTUS_VERSION.tar.gz.asc -o $SOURCE_DIR/notus-scanner-$NOTUS_VERSION.tar.gz.asc

```
Verifying the source files[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id91 "Link to this code")

```sh
gpg --verify $SOURCE_DIR/notus-scanner-$NOTUS_VERSION.tar.gz.asc $SOURCE_DIR/notus-scanner-$NOTUS_VERSION.tar.gz
```

> [!NOTE]
> The output of the last command should be similar to:
> 
> gpg: Signature made Fri Apr 16 08:31:02 2021 UTC
> gpg:                using RSA key 9823FAA60ED1E580
> gpg: Good signature from "Greenbone Community Feed integrity key" [ultimate]

If the signatures are valid, the tarballs can be extracted.

```sh
tar -C $SOURCE_DIR -xvzf $SOURCE_DIR/notus-scanner-$NOTUS_VERSION.tar.gz
```


Installing notus-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id92 "Link to this code")

```sh
cd $SOURCE_DIR/notus-scanner-$NOTUS_VERSION

mkdir -p $INSTALL_DIR/notus-scanner

python3 -m pip install --root=$INSTALL_DIR/notus-scanner --no-warn-script-location .

sudo cp -rv $INSTALL_DIR/notus-scanner/* /
```

### greenbone-feed-sync[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#greenbone-feed-sync "Link to this heading")

The `greenbone-feed-sync` tool is a Python based script to download all [feed data](https://greenbone.github.io/docs/latest/glossary.html#term-Feed) from the [Greenbone Community Feed](https://greenbone.github.io/docs/latest/glossary.html#term-Greenbone-Community-Feed) to your local machine. It is an improved version of two former shell scripts.

> [!NOTE]
> 
> _greenbone-feed-sync_ is released independently of the Greenbone Community Edition. Therefore, the newest version is used.


Required dependencies for greenbone-feed-sync[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id94 "Link to this code")

```sh
sudo apt install -y \
  python3 \
  python3-pip
```

The latest version of _greeenbone-feed-sync_ can be installed by using standard Python installation tool _pip_.

To install it system-wide for all users without running _pip_ as root user, the following commands can be used:


Installing greenbone-feed-sync system-wide for all users[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id96 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/greenbone-feed-sync

python3 -m pip install --root=$INSTALL_DIR/greenbone-feed-sync --no-warn-script-location greenbone-feed-sync

sudo cp -rv $INSTALL_DIR/greenbone-feed-sync/* /
```

### gvm-tools[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#gvm-tools "Link to this heading")

The Greenbone Vulnerability Management Tools, or _gvm-tools_ in short, are a collection of tools that help with controlling Greenbone Community Edition installations or Greenbone Enterprise Appliances remotely.

Essentially, the tools aid accessing the communication protocols [Greenbone Management Protocol (GMP)](https://greenbone.github.io/docs/latest/glossary.html#term-GMP) and [Open Scanner Protocol (OSP)](https://greenbone.github.io/docs/latest/glossary.html#term-OSP).

_gvm-tools_ are optional and not required for a functional GVM stack.

> [!NOTE]
> 
> _gvm-tools_ is released independently of the Greenbone Community Edition. Therefore, the newest version is used.


Required dependencies for gvm-tools[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id98 "Link to this code")

```sh
sudo apt install -y \
  python3 \
  python3-pip \
  python3-venv \
  python3-setuptools \
  python3-packaging \
  python3-lxml \
  python3-defusedxml \
  python3-paramiko
```
The latest version of _gvm-tools_ can be installed for each user via the standard Python installation tool _pip_.

Alternatively to install it system-wide without running _pip_ as root user, the following commands can be used:


Installing gvm-tools system-wide[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id100 "Link to this code")

```sh
mkdir -p $INSTALL_DIR/gvm-tools

python3 -m pip install --root=$INSTALL_DIR/gvm-tools --no-warn-script-location gvm-tools

sudo cp -rv $INSTALL_DIR/gvm-tools/* /
```

## Performing a System Setup[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#performing-a-system-setup "Link to this heading")

### Setting up the Redis Data Store[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-up-the-redis-data-store "Link to this heading")

Looking at the [Architecture](https://greenbone.github.io/docs/latest/architecture.html), the [Redis](https://redis.io/) key/value storage is used by the scanner (_openvas-scanner_ and _ospd-openvas_) for handling the [VT](https://greenbone.github.io/docs/latest/glossary.html#term-VT) information and scan results.


Installing the Redis server[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id102 "Link to this code")

```sh
sudo apt install -y redis-server
```

After installing the Redis server package, a specific configuration for the _openvas-scanner_ must be added.

Adding configuration for running the Redis server for the scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id104 "Link to this code")

```sh
sudo cp $SOURCE_DIR/openvas-scanner-$OPENVAS_SCANNER_VERSION/config/redis-openvas.conf /etc/redis/
sudo chown redis:redis /etc/redis/redis-openvas.conf
echo "db_address = /run/redis-openvas/redis.sock" | sudo tee -a /etc/openvas/openvas.conf
```

Start redis with openvas config[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id105 "Link to this code")

```sh
sudo systemctl start redis-server@openvas.service
```

Ensure redis with openvas config is started on every system startup[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id106 "Link to this code")

sudo systemctl enable redis-server@openvas.service

Additionally the _gvm_ user must be able to access the redis unix socket at `/run/redis-openvas/redis.sock`.

Adding the _gvm_ user to the redis group[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id107 "Link to this code")

```sh
sudo usermod -aG redis gvm
```

### Setting up the Mosquitto MQTT Broker[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-up-the-mosquitto-mqtt-broker "Link to this heading")

The Mosquitto MQTT broker is used for communication between _ospd-openvas_, _openvas-scanner_ and _notus-scanner_.


Installing the Mosquitto broker[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id108 "Link to this code")

```sh
sudo apt install -y mosquitto
```

After installing the Mosquitto broker package, the broker must be started and the server uri must be added to the _openvas-scanner_ configuration.

Starting the broker and adding the server uri to the openvas-scanner configuration[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id110 "Link to this code")

```sh
sudo systemctl start mosquitto.service
sudo systemctl enable mosquitto.service
echo -e "mqtt_server_uri = localhost:1883\ntable_driven_lsc = yes" | sudo tee -a /etc/openvas/openvas.conf
```

### Adjusting Permissions[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#adjusting-permissions "Link to this heading")

For a system-wide multi-user installation, it must be ensured that the directory permissions are set correctly and are matching the group setup. All users of the group _gvm_ should be able to read and write logs, lock files and data like VTs.

Adjusting directory permissions[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id111 "Link to this code")

```sh
sudo mkdir -p /var/lib/notus
sudo mkdir -p /run/gvmd

sudo chown -R gvm:gvm /var/lib/gvm
sudo chown -R gvm:gvm /var/lib/openvas
sudo chown -R gvm:gvm /var/lib/notus
sudo chown -R gvm:gvm /var/log/gvm
sudo chown -R gvm:gvm /run/gvmd

sudo chmod -R g+srw /var/lib/gvm
sudo chmod -R g+srw /var/lib/openvas
sudo chmod -R g+srw /var/log/gvm
```

To allow all users of the group _gvm_ access to the postgres database via the various _gvmd_ commands, the permissions of the _gvmd_ executable will be adjusted to always run as the _gvm_ user and under the _gvm_ group.

Adjusting gvmd permissions[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id112 "Link to this code")

```sh
sudo chown gvm:gvm /usr/local/sbin/gvmd
sudo chmod 6750 /usr/local/sbin/gvmd
```

### Feed Validation[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#feed-validation "Link to this heading")

For validating the feed content, a GnuPG keychain with the _Greenbone Community Feed integrity key_ needs to be created.

Creating a GPG keyring for feed content validation[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id113 "Link to this code")

```sh
curl -f -L https://www.greenbone.net/GBCommunitySigningKey.asc -o /tmp/GBCommunitySigningKey.asc

export GNUPGHOME=/tmp/openvas-gnupg
mkdir -p $GNUPGHOME

gpg --import /tmp/GBCommunitySigningKey.asc
echo "8AE4BE429B60A59B311C2E739823FAA60ED1E580:6:" | gpg --import-ownertrust

export OPENVAS_GNUPG_HOME=/etc/openvas/gnupg
sudo mkdir -p $OPENVAS_GNUPG_HOME
sudo cp -r /tmp/openvas-gnupg/* $OPENVAS_GNUPG_HOME/
sudo chown -R gvm:gvm $OPENVAS_GNUPG_HOME
```

### Setting up _sudo_ for Scanning[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-up-sudo-for-scanning "Link to this heading")

For vulnerability scanning, it is required to have several capabilities for which only root users are authorized, e.g., creating raw sockets. Therefore, a configuration will be added to allow the users of the _gvm_ group to run the _openvas-scanner_ application as root user via _sudo_.

> [!Warning]
> 
> Make sure that only necessary users have access to the _gvm_ group. Each user of the _gvm_ group can manipulate the Vulnerability Test (VT) scripts (.nasl files). These scripts are run with root privileges and therefore can be used for exploits. See [https://csal.medium.com/pentesters-tricks-local-privilege-escalation-in-openvas-fe933d7f161f](https://csal.medium.com/pentesters-tricks-local-privilege-escalation-in-openvas-fe933d7f161f).


```
sudo visudo


...

# allow users of the gvm group run openvas
%gvm ALL = NOPASSWD: /usr/local/sbin/openvas
```


### Setting up PostgreSQL[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-up-postgresql "Link to this heading")

The [PostgreSQL](https://www.postgresql.org/) database management system is used as a central storage for user and scan information. _gvmd_ connects to a PostgreSQL database and queries the data. This database must be created and configured.


Installing the PostgreSQL server[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id114 "Link to this code")

```sh
sudo apt install -y postgresql
```

If necessary the PostgreSQL database server needs to be started manually


Starting the PostgreSQL database server[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id116 "Link to this code")

```sh
sudo systemctl start postgresql@14-main
```

For setting up the PostgreSQL database it is required to become the postgres user.

Changing to the postgres user[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id119 "Link to this code")

```sh
sudo -u postgres bash
```


Setting up PostgreSQL user and database for the Greenbone Community Edition[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id120 "Link to this code")

```sh
cd
createuser -DRS gvm
createdb -O gvm gvmd
```

Setting up database permissions and extensions[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id121 "Link to this code")

```sh
psql gvmd -c "create role dba with superuser noinherit; grant dba to gvm;"

exit
```

### Setting up an Admin User[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-up-an-admin-user "Link to this heading")

For accessing and configuring the vulnerability data, an administrator user needs to be created. This user can log in via the Greenbone Security Assistant (GSA) web interface. They will have access to all data and will later be configured to act as the _Feed Import Owner_.

> [!NOTE]
> There are two options below.  You need to pick one of them.  This is where I've been having trouble. 
> 

Creating an administrator user with generated password[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id122 "Link to this code")

```sh
/usr/local/sbin/gvmd --create-user=admin
```

The new administrator user’s password is printed on success. An administrator user can later create further users or administrators via the GSA web interface.

To create the administrator user with a password of your choice instead of the generated password, the following command can be used:

Creating an administrator user with provided password[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id123 "Link to this code")

```sh
/usr/local/sbin/gvmd --create-user=admin --password=<password>
```

If the output doesn’t show

User created.

you need to look at the `/var/log/gvm/gvmd.log` for errors.

### Setting the Feed Import Owner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-the-feed-import-owner "Link to this heading")

Certain resources that were previously part of the gvmd source code are now shipped via the feed. An example is the scan configuration “Full and Fast”.

Currently every resource needs an owner to apply the permissions and manage the access to the resources.

Therefore, _gvmd_ will only create these resources if a _Feed Import Owner_ is configured. Here the previously created admin user will be used as the _Feed Import Owner_.

Setting the Feed Import Owner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id124 "Link to this code")

```sh
/usr/local/sbin/gvmd --modify-setting 78eceaec-3385-11ea-b237-28d24461215b --value `/usr/local/sbin/gvmd --get-users --verbose | grep admin | awk '{print $2}'`
```

### Setting up Services for _Systemd_[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#setting-up-services-for-systemd "Link to this heading")

[Systemd](https://systemd.io/) is used to start the daemons _ospd-openvas_, _notus-scanner_, _gvmd_ and _gsad_. Therefore, service files are required.

Systemd service file for ospd-openvas[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id125 "Link to this code")

```sh
cat << EOF > $BUILD_DIR/ospd-openvas.service
[Unit]
Description=OSPd Wrapper for the OpenVAS Scanner (ospd-openvas)
Documentation=man:ospd-openvas(8) man:openvas(8)
After=network.target networking.service redis-server@openvas.service mosquitto.service
Wants=redis-server@openvas.service mosquitto.service notus-scanner.service
ConditionKernelCommandLine=!recovery

[Service]
Type=exec
User=gvm
Group=gvm
RuntimeDirectory=ospd
RuntimeDirectoryMode=2775
PIDFile=/run/ospd/ospd-openvas.pid
ExecStart=/usr/local/bin/ospd-openvas --foreground --unix-socket /run/ospd/ospd-openvas.sock --pid-file /run/ospd/ospd-openvas.pid --log-file /var/log/gvm/ospd-openvas.log --lock-file-dir /var/lib/openvas --socket-mode 0o770 --mqtt-broker-address localhost --mqtt-broker-port 1883 --notus-feed-dir /var/lib/notus/advisories
SuccessExitStatus=SIGKILL
Restart=always
RestartSec=60

[Install]
WantedBy=multi-user.target
EOF
```

Install systemd service file for ospd-openvas[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id126 "Link to this code")

```sh
sudo cp -v $BUILD_DIR/ospd-openvas.service /etc/systemd/system/
```

Systemd service file for notus-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id127 "Link to this code")

```sh
cat << EOF > $BUILD_DIR/notus-scanner.service
[Unit]
Description=Notus Scanner
Documentation=https://github.com/greenbone/notus-scanner
After=mosquitto.service
Wants=mosquitto.service
ConditionKernelCommandLine=!recovery

[Service]
Type=exec
User=gvm
RuntimeDirectory=notus-scanner
RuntimeDirectoryMode=2775
PIDFile=/run/notus-scanner/notus-scanner.pid
ExecStart=/usr/local/bin/notus-scanner --foreground --products-directory /var/lib/notus/products --log-file /var/log/gvm/notus-scanner.log
SuccessExitStatus=SIGKILL
Restart=always
RestartSec=60

[Install]
WantedBy=multi-user.target
EOF
```

Install systemd service file for notus-scanner[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id128 "Link to this code")

```sh
sudo cp -v $BUILD_DIR/notus-scanner.service /etc/systemd/system/
```

Systemd service file for gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id129 "Link to this code")

```sh
cat << EOF > $BUILD_DIR/gvmd.service
[Unit]
Description=Greenbone Vulnerability Manager daemon (gvmd)
After=network.target networking.service postgresql.service ospd-openvas.service
Wants=postgresql.service ospd-openvas.service
Documentation=man:gvmd(8)
ConditionKernelCommandLine=!recovery

[Service]
Type=exec
User=gvm
Group=gvm
PIDFile=/run/gvmd/gvmd.pid
RuntimeDirectory=gvmd
RuntimeDirectoryMode=2775
ExecStart=/usr/local/sbin/gvmd --foreground --osp-vt-update=/run/ospd/ospd-openvas.sock --listen-group=gvm
Restart=always
TimeoutStopSec=10

[Install]
WantedBy=multi-user.target
EOF
```

Install systemd service file for gvmd[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id130 "Link to this code")

```sh
sudo cp -v $BUILD_DIR/gvmd.service /etc/systemd/system/
```

Systemd service file for gsad[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id131 "Link to this code")

```sh
cat << EOF > $BUILD_DIR/gsad.service
[Unit]
Description=Greenbone Security Assistant daemon (gsad)
Documentation=man:gsad(8) https://www.greenbone.net
After=network.target gvmd.service
Wants=gvmd.service

[Service]
Type=exec
User=gvm
Group=gvm
RuntimeDirectory=gsad
RuntimeDirectoryMode=2775
PIDFile=/run/gsad/gsad.pid
ExecStart=/usr/local/sbin/gsad --foreground --listen=127.0.0.1 --port=9392 --http-only
Restart=always
TimeoutStopSec=10

[Install]
WantedBy=multi-user.target
Alias=greenbone-security-assistant.service
EOF
```

Install systemd service file for gsad[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id132 "Link to this code")

```sh
sudo cp -v $BUILD_DIR/gsad.service /etc/systemd/system/
```

Afterwards, the services need to be activated and started.

Making systemd aware of the new service files[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id133 "Link to this code")

```sh
sudo systemctl daemon-reload
```

## Performing a Feed Synchronization[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#performing-a-feed-synchronization "Link to this heading")

For the actual vulnerability scanning, [Vulnerability Test scripts](https://greenbone.github.io/docs/latest/glossary.html#term-VT), security information like [CVEs](https://greenbone.github.io/docs/latest/glossary.html#term-CVE), port lists and scan configurations are required. All this data is provided by the [Greenbone Community Feed](https://greenbone.github.io/docs/latest/glossary.html#term-Greenbone-Community-Feed) and should be downloaded initially before starting the services.

A synchronization always consists of two parts:

1. Downloading the changes via [rsync](https://en.wikipedia.org/wiki/Rsync)
    
2. Loading the changes into memory and a database by a daemon
    

Both steps may take a while, from several minutes up to hours, especially for the initial synchronization. Only if both steps are finished, the synchronized data is up-to-date and can be used.

The first step is done via the **greenbone-feed-sync** script. The second step is done automatically when the daemons are started.

### Downloading the Data[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#downloading-the-data "Link to this heading")

> [!NOTE]
> 
> 
> Downloading the data during the synchronization may take a while depending on the network connection and server resources.
> 
> The downloaded data consist of four different kind of data:
> 
> - VT data
>     
> - SCAP data
>     
> - CERT data
>     
> - GVMD data
>     
> 
> VT data contain `.nasl` and `.notus` files for creating results during a vulnerability scan. The `.nasl` files are processed by the OpenVAS Scanner and the `.notus` files by the [Notus Scanner](https://greenbone.github.io/docs/latest/glossary.html#term-notus-scanner).
> 
> [SCAP](https://greenbone.github.io/docs/latest/glossary.html#term-SCAP) data contains [CPE](https://greenbone.github.io/docs/latest/glossary.html#term-CPE) and [CVE](https://greenbone.github.io/docs/latest/glossary.html#term-CVE) information.
> 
> CERT data contain vulnerability information from the German [DFN-CERT](https://www.dfn-cert.de/) and [CERT-Bund](https://www.bsi.bund.de/EN/Themen/Unternehmen-und-Organisationen/Cyber-Sicherheitslage/Reaktion/CERT-Bund/cert-bund_node.html) agencies.
> 
> GVMD data (or also called “data objects”) are scan configurations, compliance policies, port lists and report formats.

Downloading the data from the Greenbone Community Feed[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id134 "Link to this code")


> [!NOTE] NOTE
> This next step may take a long time!!  go make a cuppa and grab a biscuit. [www.nicecupofteaandasitdown.com](http://www.nicecupofteaandasitdown.com/)
> I started mine at 10:52 am
> it finished at 11:30



```sh
sudo /usr/local/bin/greenbone-feed-sync
```

### Starting the Greenbone Community Edition Services[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#starting-the-greenbone-community-edition-services "Link to this heading")

> [! Important]
> 
> When the feed content has been downloaded, the new data must be loaded by the corresponding daemons. This may take several minutes up to hours, especially for the initial loading of the data. Without loaded data, scans will contain incomplete and erroneous results.

 After starting the Greenbone Community Edition services via systemd, the running daemons will pick up the feed content and load the data automatically.

> [!Warning]
> 
> Please be aware, even if the **systemctl start** commands are returning immediately, the **first startup** of the services may take several minutes or even hours!

Finally starting the services[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id135 "Link to this code")

```sh
sudo systemctl start notus-scanner
sudo systemctl start ospd-openvas
sudo systemctl start gvmd
sudo systemctl start gsad
```

Ensuring services are run at every system startup[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id136 "Link to this code")

```sh
sudo systemctl enable notus-scanner
sudo systemctl enable ospd-openvas
sudo systemctl enable gvmd
sudo systemctl enable gsad
```

Checking the status of the services[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id137 "Link to this code")

```sh
sudo systemctl status notus-scanner
sudo systemctl status ospd-openvas
sudo systemctl status gvmd
sudo systemctl status gsad
```

### Vulnerability Tests Data[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#vulnerability-tests-data "Link to this heading")

> [!NOTE]
> There is only one more copy and paste! and it is at the very end.  The "code" snippets here are system messages that are expected.
> 

If the log file of ospd-openvas (`/var/log/gvm/ospd-openvas.log`) contains the following output, the OpenVAS Scanner starts to load the new VT data:

ospd-openvas VT loading log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id138 "Link to this code")

`Loading VTs. Scans will be [requested|queued] until VTs are loaded. This may`
`take a few minutes, please wait ...`

The loading of the VT data is finished if the following log message can be found:

ospd-openvas VTs loading finished log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id139 "Link to this code")

`Finished loading VTs. The VT cache has been updated from version X to Y.`

After the scanner is aware of the VT data, the data will be requested by gvmd. This will result in the following log message in `/var/log/gvm/gvmd.log`:

gvmd VTs loading log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id140 "Link to this code")

`OSP service has different VT status (version X) from database (version (Y), Z VTs). Starting update ...`

When gvmd has finished loading all VTs, the following message appears:

gvmd VTs loading finished log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id141 "Link to this code")

`Updating VTs in database ... done (X VTs).`

### SCAP Data[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#scap-data "Link to this heading")

gvmd starts loading the [SCAP](https://greenbone.github.io/docs/latest/glossary.html#term-SCAP) data containing [CPE](https://greenbone.github.io/docs/latest/glossary.html#term-CPE) and [CVE](https://greenbone.github.io/docs/latest/glossary.html#term-CVE) information when the following message can be found in the logs (`/var/log/gvm/gvmd.log`):

gvmd SCAP data loading log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id142 "Link to this code")

`update_scap: Updating data from feed`

The SCAP data is loaded and the synchronization is finished when the (gvmd) log contains the following message:

gvmd SCAP data loading finished log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id143 "Link to this code")

`update_scap_end: Updating SCAP info succeeded`

### CERT Data[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#cert-data "Link to this heading")

gvmd starts loading the CERT data containing DFN-CERT and CERT-Bund advisories when the following message can be found in the logs (`/var/log/gvm/gvmd.log`):

gvmd CERT data loading log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id144 "Link to this code")

``sync_cert: Updating data from feed``

The CERT data is loaded and the synchronization is finished when the (gvmd) log contains the following message:

gvmd CERT data finished loading log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id145 "Link to this code")

``sync_cert: Updating CERT info succeeded.``

### GVMD Data[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#gvmd-data "Link to this heading")

The log (`/var/log/gvm/gvmd.log`) contains several messages when the gvmd data is loaded. For port lists, these messages are similar to:

gvmd port list loaded log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id146 "Link to this code")

 `Port list All IANA assigned TCP (33d0cd82-57c6-11e1-8ed1-406186ea4fc5) has been created by admin`

For report formats:

gvmd report format loaded log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id147 "Link to this code")

`Report format XML (a994b278-1f62-11e1-96ac-406186ea4fc5) has been created by admin`

> [!Hint]
>
> 
> Scan Configs can only be loaded if the VT data is available in gvmd and a _Feed Import Owner_ is set.

For scan configs:

gvmd scan config loaded log message[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id148 "Link to this code")

`Scan config Full and fast (daba56c8-73ec-11df-a475-002264764cea) has been created by admin`

## Starting the Vulnerability Management[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#starting-the-vulnerability-management "Link to this heading")

After the services have started and all data has been loaded, the [Greenbone Security Assistant web interface – GSA –](https://greenbone.github.io/docs/latest/glossary.html#term-GSA) can be opened in the browser.

Opening Greenbone Security Assistant in the browser[](https://greenbone.github.io/docs/latest/22.4/source-build/index.html#id149 "Link to this code")
```SH
xdg-open "http://127.0.0.1:9392" 2>/dev/null >/dev/null &
```

The browser will show the login page of _GSA_ and after using the credentials created in the _Setting Up an Admin User_ chapter, it is possible to start with the vulnerability scanning.



## Accessing the web interface through ssh[](https://techblog.dev/posts/2023/02/fix-x11-connection-rejected-wrong-authentication-openssh-error-linux/ "Link to this heading")

> [!NOTE] Note
> The following are the instructions to enable access to the local web address using X Server through SSH

First configure the server with openVAS (remote server)
```sh
ssh user@xxx.xxx.xxx.xxx
```

Install xauth.

```sh
sudo apt install xauth
```

Enable forwarding for the X Server

```sh
sudo nano /etc/ssh/sshd_config
```

In the config file find and ensure the following are  set

```nano
	X11Forwarding yes
	X11UseLocalhost no
	X11DisplayOffset 10
	AllowTcpForwarding yes

```

save the config file.

```sh
sudo service ssh restart

exit
```

you should now be back on your local machine (not the openvas server)

```sh
sudo apt install xauth

sudo nano /etc/ssh/sshd_config
```

This needs to have the following set.

```nano
	ForwardAgent yes
	ForwardX11 yes
	ForwardX11Trusted yes
```

Exit out saving the config file.

```sh
sudo servive ssh restart
```

and log back into the openvas server....

> [!NOTE] use -X
> This time we will log in using the ssh -X to enable X11 forwarding


```sh
ssh -X user@xxx.xxx.xxx.xxx
```


Now using a command on the server such as:
```sh
firefox http://127.0.0.1:9392
```

This should load a firefox browser on your local GUI machine.
If not check the following:

```sh
echo $XAUTHORITY
```

if it returns a blank you need to set it.

```sh
export $XAUTHORITY=$HOME/.Xauthority
```
