# Getting Started with Nokogiri

Parsing XML and HTML is something most programmers 

FUCK!!!!!!  INRO GOES HERE

## Installation

Nokogiri is actually a wrapper around the excellent HTML/XML parsing library
written by [Daniel Veillard called libxml2][http://xmlsoft.org/].  Since
Nokogiri simply wraps and builds upon this already existing library,
installing libxml2 is a prerequisite for installing Nokogiri.  Fortunately,
libxml2 has been ported to most systems, so installing libxml2 should be
pretty easy.

### OS X

I recommend installing libxml2 on OS X from [macports][http://macports.org].
OS X ships with libxml2 installed, but macports is more up to date, so I
recommend using it instead.

To install libxml2 from macports do this:

    $ sudo port install libxml2 libxslt

Then to install nokogiri:

    $ sudo gem install nokogiri

That should be it!

### Linux

On Linux, we still need to install libxml2.  The command for installing
libxml2 will change depending on the package manager and linux distribution
you're using, but we'll cover Fedora and Ubuntu here.

On Fedora:

    $ sudo yum install libxml2-devel libxslt-devel
    $ gem install nokogiri

On Ubuntu

    $ sudo apt-get install libxml2 libxml2-dev libxslt libxslt-dev
    $ gem install nokogiri

### Windows

Dealing with libxml2 on Windows is so much work, that we've built libxml2 for
you, and ship it along with Nokogiri.  On Windows, to install, simply do

    gem install nokogiri

### Oh Noes!  Something went wrong!

Nokogiri ships with some basic intelligence for finding your installation of
libxml2.  But clever developers can easily fool it!  If you have problems,
first check that the libxml2 and libxslt development packages are installed.
If everything seems OK, and Nokogiri *still* won't install, make sure to send
an email to [the mailing list][http://groups.google.com/group/nokogiri-talk].
We're here to help!

## Basic parsing

Now that we have installation out of the way, it's time to get Nokogiri to do
some work for us.  Nokogiri lets you parse an HTML or XML document using a few
different strategies:

* DOM
* SAX
* Reader
* Pull

Each of these strategies have different advantages and disadvantages.  We
won't cover the particular differences in each, but the DOM interface is most
common and easy to use for developers, so that is what we'll use.

### Data structures

## Data Extraction

### Basic XPath

### Basic CSS
