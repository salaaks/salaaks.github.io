links to
https://jekyllrb.com/docs/installation/windows/

installing ruby to ubuntu 22.04 LTS

laaksonen@DESKTOP-5B4D6CS:~$ sudo apt-add-repository ppa:brightbox/ruby-ng
PPA publishes dbgsym, you may need to include 'main/debug' component
Repository: 'deb https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu/ jammy main'
Description:
Ubuntu packages for Ruby 2.7, 2.6, 2.5, 2.4, 2.3, 2.2, 2.1, 2.0 1.9.3 and 1.8.

See https://www.brightbox.com/docs/ruby/ubuntu/ for more details.

Discussion list here:

https://groups.google.com/forum/#!forum/brightbox-ruby-ubuntu-packaging

https://www.brightbox.com
More info: https://launchpad.net/~brightbox/+archive/ubuntu/ruby-ng
Adding repository.
Press [ENTER] to continue or Ctrl-c to cancel.
Adding deb entry to /etc/apt/sources.list.d/brightbox-ubuntu-ruby-ng-jammy.list
Adding disabled deb-src entry to /etc/apt/sources.list.d/brightbox-ubuntu-ruby-ng-jammy.list
Adding key to /etc/apt/trusted.gpg.d/brightbox-ubuntu-ruby-ng.gpg with fingerprint 80F70E11F0F0D5F10CB20E62F5DA5F09C3173AA6
Ign:1 https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy InRelease
Err:2 https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy Release
  404  Not Found [IP: 185.125.190.52 443]
Hit:3 http://security.ubuntu.com/ubuntu jammy-security InRelease
Hit:4 http://archive.ubuntu.com/ubuntu jammy InRelease
Hit:5 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:6 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
Reading package lists... Done
E: The repository 'https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.

both
sudo apt-get update

fails with
Ign:1 https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy InRelease
Err:2 https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy Release
  404  Not Found [IP: 185.125.190.52 443]
Hit:3 http://security.ubuntu.com/ubuntu jammy-security InRelease
Hit:4 http://archive.ubuntu.com/ubuntu jammy InRelease
Hit:5 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:6 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
Reading package lists... Done
E: The repository 'https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.

and
sudo apt-get install ruby2.5 ruby2.5-dev build-essential dh-autoreconf

fails with
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package ruby2.5 is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'ruby2.5' has no installation candidate
E: Unable to locate package ruby2.5-dev
E: Couldn't find any package by glob 'ruby2.5-dev'
E: Couldn't find any package by regex 'ruby2.5-dev'

reference ruby installation from https://linux.how2shout.com/3-ways-to-install-ruby-on-ubuntu-22-04-lts-jammy/

rvm get head

rvm install ruby-3.2.2


install jekyll
https://jekyllrb.com/docs/installation/ubuntu/

configure gem location
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

install jekyll and builder

gem install jekyll bundler

jekyll new --skip-bundle .

bundle install

bundle exec jekyll serve

bundle add webrick

