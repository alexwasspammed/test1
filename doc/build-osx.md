Mac OS X test1coind build instructions
====================================

Authors
-------

* Laszlo Hanyecz <solar@heliacal.net>
* Douglas Huff <dhuff@jrbobdobbs.org>
* Colin Dean <cad@cad.cx>
* Gavin Andresen <gavinandresen@gmail.com>
* Alan Westbrook

License
-------

Copyright (c) 2009-2012 Bitcoin Developers

Distributed under the MIT/X11 software license, see the accompanying
file COPYING or http://www.opensource.org/licenses/mit-license.php.

This product includes software developed by the OpenSSL Project for use in
the OpenSSL Toolkit (http://www.openssl.org/).

This product includes cryptographic software written by
Eric Young (eay@cryptsoft.com) and UPnP software written by Thomas Bernard.

Notes
-----

See `doc/readme-qt.rst` for instructions on building Test1Coin-Qt, the
graphical user interface.

Tested on OS X 10.6 through 10.9 on Intel processors only. PPC is not
supported because it is big-endian.

All of the commands should be executed in a Terminal application. The
built-in one is located in `/Applications/Utilities`.

Much of this may be outdated for Test1Coin.

Preparation
-----------

You need to install XCode with all the options checked so that the compiler
and everything is available in /usr not just /Developer. XCode should be
available on your OS X installation media, but if not, you can get the
current version from https://developer.apple.com/xcode/. If you install
Xcode 4.3 or later, you'll need to install its command line tools. This can
be done in `Xcode > Preferences > Downloads > Components` and generally must
be re-done or updated every time Xcode is updated.

There's an assumption that you already have `git` installed, as well. If
not, it's the path of least resistance to install [Github for Mac](https://mac.github.com/)
(OS X 10.7+) or
[Git for OS X](https://code.google.com/p/git-osx-installer/). It is also
available via Homebrew or MacPorts.

You will also need to install [Homebrew](http://mxcl.github.io/homebrew/)
or [MacPorts](https://www.macports.org/) in order to install library
dependencies. It's largely a religious decision which to choose, but, as of
December 2012, MacPorts is a little easier because you can just install the
dependencies immediately - no other work required. If you're unsure, read
the instructions through first in order to assess what you want to do.
Homebrew is a little more popular among those newer to OS X.

The installation of the actual dependencies is covered in the Instructions
sections below.

Instructions: MacPorts
----------------------

### Install dependencies

Installing the dependencies using MacPorts is very straightforward.

    sudo port install boost db48@+no_java openssl miniupnpc

### Building `test1coind`

1. Clone the github tree to get the source code and go into the directory.

        git clone git@github.com:test1coin-project/test1coin.git test1coin
        cd test1coin

2.  Build test1coind:

        cd src
        make -f makefile.osx

3.  It is a good idea to build and run the unit tests, too:

        make -f makefile.osx test

Instructions: HomeBrew
----------------------

#### Install dependencies using Homebrew

        brew install boost miniupnpc openssl berkeley-db

Note: After you have installed the dependencies, you should check that the Brew installed version of OpenSSL is the one available for compilation. You can check this by typing

        openssl version

into Terminal. You should see OpenSSL 1.0.1g 7 Apr 2014.

If not, you can ensure that the Brew OpenSSL is correctly linked by running

        brew link openssl --force

Rerunning "openssl version" should now return the correct version.

For boost in test1coin, there are some ‘fun’ things you have to do:

 * download the latest boost from source

        http://www.boost.org/users/download/

 * Build the boost libs with special flags because ... REASONS!

        ./bootstrap.sh
        ./b2 --toolset=clang cxxflags="-stdlib=libstdc++" linkflags="-stdlib=libstdc++"   variant=release link=static threading=multi runtime-link=static --build-dir=build   --build-type=minimal stage --with-program_options --with-system --with-filesystem   --with-chrono --with-thread

 * link the statics to /usr/local/lib/
 * make sure the headers for boost are in /usr/local/include/boost or linked from there.

### Building `test1coind`

1. Clone the github tree to get the source code and go into the directory.

        git clone git@github.com:test1coin/test1coin.git test1coin
        cd test1coin

2.  Build test1coind:

        cd src
        make -f makefile.osx

3.  It is a good idea to build and run the unit tests, too:

        make -f makefile.osx test

Creating a release build
------------------------

    make -f makefile.osx RELEASE=1

Running
-------

It's now available at `./test1coind`, provided that you are still in the `src`
directory. We have to first create the RPC configuration file, though.

Run `./test1coind` to get the filename where it should be put, or just try these
commands:

    echo -e "rpcuser=test1coinrpc\nrpcpassword=$(xxd -l 16 -p /dev/urandom)" > "/Users/${USER}/Library/Application Support/test1coin/test1coin.conf"
    chmod 600 "/Users/${USER}/Library/Application Support/test1coin/test1coin.conf"

When next you run it, it will start downloading the blockchain, but it won't
output anything while it's doing this. This process may take several hours.

Other commands:

    ./test1coind --help  # for a list of command-line options.
    ./test1coind -daemon # to start the test1coin daemon.
    ./test1coind help    # When the daemon is running, to get a list of RPC commands
