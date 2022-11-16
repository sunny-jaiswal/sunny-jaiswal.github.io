---
layout: single
title: 'How to run Jekyll 3.9 on Ubuntu 22'
date: 2022-11-16 16:00:00 +0530
categories: tutorial
---

Few months ago I updated my Linux Mint system to Linux Mint 21 which uses Ubuntu 22.04 codebase. Recently I tried to run my Jekyll based personal website but I got stuck. The issue is that Github Pages requires Jekyll 3.9 (latest one is 4.3.1), Jekyll 3.9 requires Ruby 2.7 (latest version is 3.1.2), Ruby 2.7 requires OpenSSL 1.1 (latest version is 3.0.0).


Ubuntu 22 (Linux Mint 21) does not support OpenSSL 1.1 anymore, so one needs to compile the older OpenSSL and install the older versions of Ruby and Jekyll. The following are the steps that I followed:

1. We need to first need to install OpenSSL 1.1, for this I followed steps mentioned [here](https://deanpcmad.com/2022/installing-older-ruby-versions-on-ubuntu-22-04/), with some customization

        cd
        wget https://www.openssl.org/source/openssl-1.1.1g.tar.gz
        tar zxvf openssl-1.1.1g.tar.gz
        cd openssl-1.1.1g
        ./config --prefix=$HOME/.openssl/openssl-1.1.1g --openssldir=$HOME/.openssl/openssl-1.1.1g
        make
        make test
        make install

2. Now that we have installed the OpenSSL, we need to include the latest certificates, so we first remove the existing certificates and link the certificates avaiable in our system

        rm -rf ~/.openssl/openssl-1.1.1g/certs
        ln -s /etc/ssl/certs ~/.openssl/openssl-1.1.1g/certs

3. Now we can install Ruby 2.7.1 while using the above installed version of OpenSSL

        sudo apt install rbenv
        RUBY_CONFIGURE_OPTS=--with-openssl-dir=$HOME/.openssl/openssl-1.1.1g rbenv install 2.7.1
        rbenv global 2.7.1
        rbenv local 2.7.1

4. We need to update file ~/.bashrc to enable rbenv shell integration. To do this we need to append the following line in the file (sudo nano ~/.bashrc)

        eval "$(rbenv init -)"

5. We need to close and open the terminal again for the changes to reflect and verify using command:

        ruby -v

6. Now we can install bundler and jekyll

        sudo gem install bundler jekyll

7. And finally install the required gems for our project and start the local server

        cd <project-dir>
        bundle install
        bundle exec jekyll serve