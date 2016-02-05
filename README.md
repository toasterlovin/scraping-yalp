# Scraping For Leads

Let's just say you want to use a website whose name sounds a lot like Yalp to build a list of leads. That might
be against the terms of service of a site like that, but we're just talking hypotheticals here. So in this
hypothetical situation, you could manually click through the pages of results, copying and pasting company
information into a spreadsheet. But that sounds like a bunch of boring, repetitive work and boring, repetitive
work should be done by computers, not humans.

Well, in that hypothetical situation, this tool might save you a lot of time.

## Installation

```
cd ~
git clone git@github.com:toasterlovin/scraping-yalp.git
cd scraping-yalp
```

Then, if you're all setup to run Ruby stuff on your computer just do:

```
bundle install
```

If you want to do everything in a virtual machine using Vagrant, do this instead:

```
vagrant up
vagrant ssh       # now you're inside the virtual machine
cd /vagrant
bundle install
```

## Usage

If you're running this directly on your machine, you'll do this:

```
cd ~/scraping-yalp
rake yalp:scrape search='Beauty Salon' location='Portland, OR' output='beauty_salons.csv'
```

Otherwise, to run this inside the Vagrant virtual machine, do this:

```
cd ~/scraping-yalp
vagrant up        # if the virtual machine isn't already running
vagrant ssh
cd /vagrant
rake yalp:scrape search='Beauty Salon' location='Portland, OR' output='beauty_salons.csv'
```

That'll generate a list of beauty salons in Portland, OR and output it to a CSV file
called `beauty_salons.csv`. Change the options to suit your fancy.

## How it works

This is a Rake task that uses Capybara and Poltergeist/PhantomJS to simulate visiting
a website with a name that sounds an awful lot like Yalp, extracting company data as it
goes. You can modify it as needed to suit other, similar purposes.

Capybara is a tool that is normally used to test web applications by simulating a visitor
who interacts with various aspects of the application. Here we're definitely interacting
with various aspects of a web app. We just also happen to be extracting data as we go, which
is somethign that Capybara is perfectly capable of.

Poltergeist is driver that allows Capybara to use PhantomJS, which is a headless web browser
which supports Javascript. A headless browser is simply a browser that runs and operates
exactly like a normal web browser, it just doesn't actually display the web pages that it's
visiting. And javascript support is important because many features of modern sites don't
work without Javascript.


## License

You are free to do whatever you'd like with this. Just don't blame me if you get in trouble.
