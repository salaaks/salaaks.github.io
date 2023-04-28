---
layout:     post
title:      "How-to: Use WSL to manage jekyll and create Github pages"
date:       2023-04-25 11:27:00 +0300
categories: posts
---

## Introduction

On this post we will be using Windows Subsystem for Linux to create a simple personal webpage managed with jekyll and hosted with Github pages.

Why this particular combination?

*GitHub*
- Many developers have their public personal projects hosted on Github
- It is favorable to focus your developer identity in a single service (even though sites like LinkedIn and Twitter can be beneficial, but that in itself is a topic for another post).
- Github pages can be utilized by organizations too, so learning this may have positive effect on your career in that way too

*Jekyll*
- Used by Github and they provide their own additional documentation on its usage that can be utilized for further reading

*Windows Subsystem for Linux*
- Most of my personal experience so far has been using WSL as the "backend" for running Docker Desktop on Windows, so I want to familiarise myself with it more
- I'm firm believer of encapsulation programming paradigm, and in this case we get a chance to apply it on tools. Using WSL we don't have to install all dependencies on our core operating system. There are alternative ways to achieve encapsulation like VirtualBox, but checking the capabilities of WSL may allow us to use it as a replacement in the future too

It is rather common that documentation and instructions don't correspond one-to-one on your particular situation and problem. That is why on this post we document all the steps we ended up taking, even if some were deadends. This hopefully helps someone in the future when they find this post through some googling.

## Solution

### Prerequisites

These are considered as mandatory requirements to follow up with the contents of this post. For practical reasons we don't include instructions on how to enable WSL and instead leave that as practice for the reader.

- Windows Subsystem for Linux ([Official instructions][windows-subsystem-for-linux])
- Github account ([Github][github])

### Github

The following chapters are listed as they are necessary for the solution to work. But as the focus of this post is not on _how to use github_ they are handled through external links to proper resources.

#### Create repository

Everything hosted using Github pages require a repository in Github. This repository can either be public or private, but for private repository to work with Github pages you need a paid plan.

Instructions for this can be found on official [documentation][github-create-repository].

After you have successfully created your repository, clone it locally to the directory of your choosing on your own computer.

#### Use repository as site source

It is not enough to have your site stored in a repository on Github. You also need to configure it properly, so Github can perform automatic Github Actions on the source and deploy it for you.

Instructions for this can be found [here][github-configure-site].

## Setting up local tools

Now that prerequisites and Github configurations are done we can start tackling the actual problem. So what is actually needed to create Github Pages locally on Windows using jekyll?

### Installing Ubuntu 22.04 on WSL

Start by opening Microsoft Store (e.g. pressing WIN+Q and typing "store" and pressing enter)

On Microsoft Store use the search field and type "ubuntu". This will most likely provide you with multiple search results. Find the one named "Ubuntu 22.04 LTS" and click on it.

Now find "Install" -button and click it. Depending on the speed of your network connection this may take a while.

Once the intallation is complete, the "Install" -button should have change to "Open" -button.

Clicking "Open" will automatically trigger opening of new terminal window.

As your first task, you need to define username & password for this Ubuntu installation. Remember this password as you will need it for sudo commands.

Now the Ubuntu 22.04 LTS installation is ready for use.

### Installing jekyll

From the official Github documentation [page][github-pages-jekyll] we can find that jekyll is based on Ruby and it is suggested to be run using Bundler.

Checking the jekyllrb installation [instructions][jekyll-rb-requirements] we can see that at minimum Ruby version 2.5 is required. Given that we are attempting to learn here, we will pick this as our starting point.

It is worth noting that as we are running Ubuntu on Windows Subsystem for Linux we can follow the steps available [here][jekyll-install-windows]. Unfortunately if we attempt to install the Ruby version 2.5 required by jekyll, we end up with issues.

Attempting to run
{% highlight bash %}
sudo apt-add-repository ppa:brightbox/ruby-ng
{% endhighlight %}
will end up printing
{% highlight bash %}
...
E: The repository 'https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
{% endhighlight %}
As there exists no such Ruby release on brightbox repository for Ubuntu 22.04.

Running the following commands listed are just as useless as we can see by following the [instructions][jekyll-install-windows] and attempting to run
{% highlight bash %}
sudo apt-get update
{% endhighlight %}
it will provide us with the same error:

{% highlight bash %}
...
E: The repository 'https://ppa.launchpadcontent.net/brightbox/ruby-ng/ubuntu jammy Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
{% endhighlight %}

On the otherhand we can try to be brave and attempt to install Ruby 2.5 using
{% highlight bash %}
sudo apt-get install ruby2.5 ruby2.5-dev build-essential dh-autoreconf
{% endhighlight %}
This will provide us with the following error
{% highlight bash %}
...
Package ruby2.5 is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'ruby2.5' has no installation candidate
E: Unable to locate package ruby2.5-dev
E: Couldn't find any package by glob 'ruby2.5-dev'
E: Couldn't find any package by regex 'ruby2.5-dev'
{% endhighlight %}

It seems that we need an other method to install Ruby.

#### Installing Ruby for jekyll

As we are still attempting to install Ruby 2.5 in hopes that it works best with the jekyll installation through some googling we find [this][install-ruby-ubuntu-22-04] page with very good installation instructions.

According to the page the 3rd method seems to give us most control over what we want to do.

So we will follow the commands from 5. step onwards.

*Install dependencies*
{% highlight bash %}
sudo apt install curl g++ gcc autoconf automake bison libc6-dev libffi-dev libgdbm-dev libncurses5-dev libsqlite3-dev libtool libyaml-dev make pkg-config sqlite3 zlib1g-dev libgmp-dev libreadline-dev libssl-dev
{% endhighlight %}

*Install GPG key*
{% highlight bash %}
gpg --keyserver hkp://pgp.mit.edu --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
{% endhighlight %}

*Get RVM*
{% highlight bash %}
curl -sSL https://get.rvm.io | bash -s stable
{% endhighlight %}

*After that source the variables*
{% highlight bash %}
source ~/.rvm/scripts/rvm
{% endhighlight %}

*Check available Ruby versions*
{% highlight bash %}
rvm list known
{% endhighlight %}

*Install Ruby 2.5*
{% highlight bash %}
rvm install ruby-2.5.8
{% endhighlight %}
Unfortunately it seems that running this ends up with an error that is related to openssl.

Further googling leads us to [this][rvm-issue] github issue page.

According to the discussion, it seems that Ubuntu 22.04 LTS no longer ships with openssl version that earlier Ruby versions depend on.

This leaves us with two options
1. add ruby 2.5 compatible version of openssl to our Ubuntu 22.04 installation
2. ditch the idea of using Ruby 2.5 and attempt to use newer version of Ruby that works automatically with Ubuntu 22.04

Checking Ruby releases from [here][ruby-releases] we can see that version 2.5.9 is released 2021-04-05, a year before our Ubuntu. So no wonder there are some issues. Lets see if we can get newer version to work better.

From the conversation part of rvm issue we can find [this][rvm-issue-solution] tidbit of knowledge.

So it seems that all we need to do is update rvm with:

{% highlight bash %}
rvm get head
{% endhighlight %}

And checking the effect of update we can run

{% highlight bash %}
rvm list known
{% endhighlight %}

to see that there are now additional Ruby versions available for installation.

Per my decision to ditch Ruby 2.5, I picked latest possible version (at time of writing this) in hopes that it would be compatible with OpenSSL version provided with Ubuntu 22.04.
{% highlight bash %}
rvm install ruby-3.2.2
{% endhighlight %}
After a while the installation is complete, and we can verify ruby version by typing.
{% highlight bash %}
ruby -v
{% endhighlight %}
This will print
{% highlight bash %}
ruby 3.2.2 (2023-03-30 revision e51014f9c0) [x86_64-linux]
{% endhighlight %}

#### Initializing jekyll project

From the jekyllrb [documentation][jekyll-install-ubuntu] we can take in to use the following:
{% highlight bash %}
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
{% endhighlight %}
this will configure the ruby gem location.

Now we can finally install jekyll and bundler
{% highlight bash %}
gem install jekyll bundler
{% endhighlight %}

After this is done, we need to navigate to our github repository location. WSL automatically mounts host operating system drives under /mnt, so if your github repository on Windows is at c:/users/admin/projects/pages you need to run

{% highlight bash %}
cd /mnt/c/users/admin/projects/pages
{% endhighlight %}

As we are initializing jekyll project to a directory that contains existing git repository we need to use --force flag. Without this flag the command fails as it detects that the given directory is not empty. As it most likely contains README.md from Github repository creation in our case. Basically the --force flag only overwrites the Gemfile in the directory.

{% highlight bash %}
jekyll new --skip-bundle . --force
{% endhighlight %}

Now we just need to make couple of adjustments, and we are ready to test our jekyll based Github page.

From the official instructions on Github [documentation][github-pages-jekyll-create] we can now utilize the steps 8 through 11.

- Open the Gemfile that Jekyll created
- Add "#" to the beginning of the line that starts with `gem "jekyll"` to comment out this line. As of writing of this post I ended up commenting out `gem "jekyll", "~> 4.3.2"`
- Add the github-pages gem by editing the line starting with `# gem "github-pages"`. Change this line to: `gem "github-pages", "~> GITHUB-PAGES-VERSION", group: :jekyll_plugins`
Replace GITHUB-PAGES-VERSION with the latest supported version of the github-pages gem. You can find this version here: ["Dependency versions."][github-pages-dependency-versions]
As of writing of this post I used ended up changing the line to `gem "github-pages", "~> 228", group: :jekyll_plugins`.
The correct version Jekyll will be installed as a dependency of the github-pages gem.
- Save and close the Gemfile.

### Testing jekyll site locally

Now that we have succesfully installed all requirements and performed necessary configurations we can test the website created by jekyll. This allows us faster iteration speed than always committing & pushing to Github and waiting for Github actions to perform deployment to internet.

From official Github pages [documentation][github-pages-jekyll-locally] we can find the following:

{% highlight bash %}
Note: If you've installed Ruby 3.0 or later (which you may have if you installed the default version via Homebrew), you might get an error at this step. That's because these versions of Ruby no longer come with webrick installed.
To fix the error, try running bundle add webrick, then re-running bundle exec jekyll serve.
{% endhighlight %}

Remembering our earlier decision to ditch Ruby 2.5 and instead use version 3.2.2 we will install webrick to avoid the issue
{% highlight bash %}
bundle add webrick
{% endhighlight %}

And now we can run the jekyll serve command using bundler to compile our page and host it locally.

{% highlight bash %}
bundle exec jekyll serve
{% endhighlight %}

By default this starts local webserver hosted on port 4000.

As we are running the Ubuntu with Windows Subsystem for Linux, the port 4000 is automatically exposed on Windows. We can now just open our favorite webbrowser and navigate to http://localhost:4000

Now we can edit our jekyll project content markdown files using either text editor on Ubuntu or on Windows, and the results are automatically available for display on our localhost browser.

## Conclusion

On this post we explored of the possibility to use WSL as "encapsulation" layer to install and manage tools used to create Github pages with jekyll static page generator. After some missteps we managed to get our toolset working and successfully tested it locally.

In the future we could encapsulate jekyll even further and create a docker image that acts as "jekyll as a service".

Hope this post was helpful to you, and thank you for reading.

-salaaks

[windows-subsystem-for-linux]: https://learn.microsoft.com/en-us/windows/wsl/install
[github]: https://github.com
[github-create-repository]: https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site#creating-a-repository-for-your-site
[github-configure-site]: https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site#creating-your-site
[github-pages-jekyll]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll
[github-pages-dependency-versions]: https://pages.github.com/versions/
[github-pages-jekyll-locally]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll#building-your-site-locally

[github-pages-jekyll-create]: https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll#creating-your-site

[jekyll-rb-requirements]: https://jekyllrb.com/docs/installation/#requirements
[jekyll-install-windows]: https://jekyllrb.com/docs/installation/windows/#installation-via-bash-on-windows-10
[jekyll-install-ubuntu]: https://jekyllrb.com/docs/installation/ubuntu/

[ruby-releases]: https://www.ruby-lang.org/en/downloads/releases/
[install-ruby-ubuntu-22-04]: https://linux.how2shout.com/3-ways-to-install-ruby-on-ubuntu-22-04-lts-jammy/

[rvm-issue]: https://github.com/rvm/rvm/issues/5209
[rvm-issue-solution]: https://github.com/rvm/rvm/issues/5209#issuecomment-1117868179