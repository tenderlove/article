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
common and easy to use for developers, so that is the interface we'll study.

There are two main entry points to Nokogiri depending on the kind of document
you wish to parse, one for HTML documents and one for XML documents.  Parsing
HTML documents looks like this:

    doc = Nokogiri::HTML(html_document)

Parsing XML documents looks like this:

    doc = Nokogiri::XML(xml_document)

Both of these functions will take an IO object *or* a String object.  Since
both forms accept IO objects, we can even feed open-uri straight in to
Nokogiri like this:

    doc = Nokogiri::HTML(open("http://www.google.com/search?q=doughnuts"))

Feeding Nokogiri an IO object is slightly more efficient than using a String,
but you should choose the one that is most convenient.

### Data structures

To become data extraction Zen Masters, we first need to understand the data
structure returned by Nokogiri.  Nokogiri converts HTML and XML documents in
to a tree data structure.

For example, an HTML document that looks like this:

    <html>
      <head>
        <title>Hello!</title>
      </head>
      <body id="uniq">
        <h1>Hello World!</h1>
      </body>
    </html>

will be represented in memory with a tree that looks like this:

![HTML Tree](html_tree.png)

Any data extraction technique we will use is simply a way for traversing this
in-memory tree.  If we keep this structure in mind while trying to do data
extraction, we can truly enter data extraction nirvana!

## Data Extraction

We've seen how to turn an HTML or XML document in to an in-memory tree.  Now
we're going to try to do something useful with this tree, namely extract some
data.  Let's take a look at a few different strategies for unlocking the data
in our tree.

We're going to look at three different ways to traverse our in-memory tree.
The first two, XPath and CSS, are small languages built specifically for tree
traversal.  The last thing we'll examine is some of the Nokogiri API for
manual tree traversal.

### Basic XPath

The [XPath language][http://www.w3.org/TR/xpath] was written for easily
traversing an XML tree structure, but we can use it with HTML trees as well.

Let's look at a sample program for extracting search result links from a google
search.  We'll use XPath to find the data we want, and we'll pick apart the
XPath syntax:

    require 'open-uri'
    require 'nokogiri'

    doc = Nokogiri::HTML(open("http://www.google.com/search?q=doughnuts"))
    doc.xpath('//h3/a').each do |node|
      puts node.text
    end

The XPath used in this program is:

    //h3/a

In English, this XPath says:

> Find all "a" tags with a parent tag whose name is "h3"

Thus, our program finds all "a" tags with "h3" parents, loops over them, and
prints out the text content.

XPath works like a directory structure where the leading "/" indicates the
root of the tree.  Slashes separate the tag matching information.  When there
is nothing between slashes, that is a sort of wild card meaning "any tag
matches".  The "h3" and "a" are tag name matchers, and only match when the tag
name matches.

Finding tag names is great, but if you run the previous program, you might
find that it returns more "a" tags than we actually want.  We need to narrow
down our search based on some attributes of the tags, specifically the "class"
values.  To match attribute values in XPath, we use brackets.  Let's look at a
couple examples.

To match "h3" tags that have a class attribute, we would say:

    h3[@class]

To match "h3" tags whose class attribute is equal to the string "r", we would
say:

    h3[@class = "r"]

Using the attribute matching construct, we can modify our previous query to:

    //h3[@class = "r"]/a[@class = "l"]

which in English terms is:

> Find all "a" tags with a class attribute equal to "l" and an immediate
> parent tag "h3" that has a class attribute equal to "r"

If we substitute that XPath back in to our original program, we'll get the
expected results.

For more information on doing XPath queries, I recommend checking out the
[tutorial at w3schools][http://www.w3schools.com/xpath/xpath_syntax.asp] as
well as the [w3 recommendation][http://www.w3.org/TR/xpath].

For more information on using XPath within Nokogiri, check out the
[Nokogiri tutorials][http://nokogiri.org/tutorials] as well as the
[RDoc][http://nokogiri.org].

Next, let's take a look at CSS syntax.

### Basic CSS

CSS is similar to XPath in that it's another language for searching a tree
data structure.  In this section, we'll perform the same task as the XPath
section, but we'll examine the CSS syntax.

CSS does not separate tag matching patterns by slashes, but rather by
whitespace or "greater than" characters (actually there are more, but here
we'll just talk about those two).  Let's rewrite our previous XPath as CSS and
examine the syntax.

    //h3/a

can be written in CSS as:

    h3 > a

The ">" character indicates that the "a" tag must be a direct descendant of
the "h3" tag.  Most CSS that I see uses space separators like this:

    h3 a

Using a space indicates that there could be any number of tags between the
"h3" tag and the "a" tag.  The space is similar to "//" in XPath, and this CSS
query could be written in XPath like this:

    //h3//a

Similar to XPath, CSS can use brackets for matching attributes.  Let's do a
couple more XPath to CSS translations.  On the left is XPath, on the right is
CSS:

    h3[@class]        =>  h3[class]
    h3[@class = "r"]  =>  h3[class = "r"]

This syntax works, but CSS provides us with a shorthand for matching the
"class" attribute.  To find all h3 tags whose class attribute contains "r",
we can say:

    h3.r

There is a subtle difference between the two previous examples.  The first one
must be an *exact match* where the second one says "the class attribute must
contain the value r".  That means only the CSS selector will match a tag like
this:

    <h3 class="r foo">Hi!</h3>

The XPath selector and our translated CSS selector would not match this tag,
but the "h3.r" selector would.  Most of the time, the CSS class selectors do
what we want.  Only when I need something very specific do I use the bracket
form in my CSS selectors.

With this knowledge in hand, we can rewrite our original program using CSS
selectors:

    doc = Nokogiri::HTML(open("http://www.google.com/search?q=doughnuts"))
    doc.css('h3.r > a.l').each do |node|
      puts node.text
    end

I think the CSS selectors usually result in clear, more concise queries than
XPath does, so I usually stick to CSS queries in my code.  There are some
tasks which CSS cannot accomplish that XPath can, so it's nice to be able to
fall back to XPath queries when I need to.

Next, let's look at some basic node API provided by Nokogiri.

### Basic Node API
