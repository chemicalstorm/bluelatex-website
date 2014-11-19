---
layout: default
title: Installation
---

* Contents
{:toc}

Installation and Start
======================

Dependencies
------------

\BlueLaTeX requires several dependencies to run:

 - The server being written in Scala you will need a JVM (version 1.7 or higher for the binary distribution, 1.6 or higher if you build it from source),
 - Users and paper data are saved in a [couchdb](http://couchdb.apache.org) database version 1.4.0 or higher,
 - You will also need to have access to a SMTP server to validate user registrations, so you might need to install one on your server if you do not want to use an external one,
 - Finally if you want to compile \LaTeX documents, you will need a \LaTeX distribution installed on your system. [TeX Live](https://www.tug.org/texlive/) is probably the best choice for Unix systems, and [MiKTeX](http://miktex.org/) for Windows.

Installation from Debian Packages
---------------------------------

We currently provide i386 and amd64 packages for debian wheezy.

To install the latest version for Debian Wheezy (7.7), run the following as root :
```shell
# cat << EOF > /etc/apt/sources.list.d/bluelatex-wheezy.list
deb http://deb.drouet.eu/ bluelatex-wheezy main
# uncomment the following line if needed
#deb-src http://deb.drouet.eu/ bluelatex-wheezy main
EOF
# wget -O - http://deb.drouet.eu/repository.asc | apt-key add -
# apt-get update

# apt-get install bluelatex
```

Installation from Binary Distribution Package
---------------------------------------------

[Download the latest version](/download/) of \BlueLaTeX, and extract the archive.

An installation script is provided in the distribution, simply run it as root:

```shell
$ cd bluelatex-<version>
$ sudo ./install.sh
```

\BlueLaTeX is installed in `/opt/bluelatex-<version>`, and the configuration is located at `/etc/opt/bluelatex`.

Installation From the Source
----------------------------

You can either download the packaged sources of the last release on the [download page](/download/) or [clone the current master](https://github.com/gnieh/bluelatex).

You need [sbt](http://scala-sbt.org) to build and package a distribution.
Then, in the root directory of the repository simply run:

```shell
$ sbt 'project blue-dist' makeDistribution
```

The dependencies will be downloaded, everything will be compiled and the distribution will be packed in `blue-dist/target/bluelatex-<version>`.

If you encounter any problem to compile \BlueLaTeX, please refer to the [developer corner](/developers/).

And Then
--------

You can now [configure](/configuration/) \BlueLaTeX to your needs and then [start it](/running/).

Upgrade
=======

A basic `upgrade.sh` script is provided in the binary archive.
It will probably run correctly if you installed a previous version of \BlueLaTeX using `install.sh` without any modification.

Otherwise, the steps to upgrade are:

 1. Stop the previously installed \BlueLaTeX version,
 3. Package the new version as described [previously](#installation-from-the-source),
 2. Deploy the new binaries and libraries (in `bin/` and `bundle/`) to the target installation directory,
 3. Make a diff between the old configuration and the new one, and adapt if necessary.
