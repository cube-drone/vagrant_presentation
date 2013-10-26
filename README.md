
Building a Development Environment using Vagrant and Chef 
---------------------------------------------------------

Hello, everyone! I'm here to talk to you about Vagrant and Chef. 

First, I'm going to tell you about the development environments 
at a few jobs I've worked at. 

First Job 
---------

My first job out of university was for a group of "web entrepreneurs".
They sold get-rich quick schemes and lose-weight-fast schemes using
a combination of high-pressure sales tactics and ridiculous promises
that they could never keep - but I'm not here to sell you on the company, 
I want to talk about their server. 

Nobody knew what was installed on it. We had a development server containing
a MySQL database with sample data, and in order to develop in an environment
that looked anything like our deployment environment, we just had to ssh
in to their development box and work from there. It was not particularly
optimal, but it did allow me to get very conversant with vim and screen
very quickly, so there's that. 

The only way to push changes to the development server was to yell at the
lead developer, who would get around to it at his own convenience.

Why do I mention this? This is an example of a development and deployment
environment that was completely out of control. This is something that
we want to move away from. 

Second Job
----------
In my second job out of university, our development environment was a 
Java stack. It had a lot of different parts, mostly composed of a 
conglomeration of JAR archives and source checkouts
(screen joke: 
    yes, I understand saying JAR archives is like saying ATM machine or PIN Number) 
with specific versions that had been blessed by a core architecture team.

Getting a working stack running took about a day of downloading and
configuring. Changing anything took a petition to the core architecture team 
and about six months of meetings and general purpose bureaucracy. A lot 
of that is not the fault of the installation procedure - doing anything
at a big telecom takes about six months. 

Why do I mention this? This is an example of a development and deployment
environment that _is_ under control. Everybody was working on the same
libraries, everybody knew all of the parts that needed to go into a deployment, 
it was very sane and manageable. The problem was that it was also a very 
manual process. It was a lot of work to manage, a lot of work to get the 
environment installed, and the whole thing was very cumbersome. Still, though, 
I'd rather that, then the previous job, where nobody had any idea what was 
going on at all. 

Fourth Job
----------
In my fourth job out of university - you'll notice I'm doing this out of 
chronological order, it's in service of a point - every developer just used
the built-in development environment in Visual Studio - the mock IIS server, 
in-memory database, and a series of C# libraries downloaded from a central 
repository - around the office there was sort of an oral tradition about how
to get things set up in the right order to get the dev environment oriented 
in the write way to actually run the software.

Taking this software and getting it to work on a proper server
stack? That was build and ops's problem. We were dev. 

This sort of thing totally works, if your ops team is solid and you don't
mind if your developers have no idea what their software is going to run on.
Just declaring that environment management is someone else's problem is 
a valid enough strategy if you have manpower to throw at it. 

Third Job
---------
In my third job out of university, they had a very complicated environment.
The system was built out of PHP, and it had been around for almost 10 years. 
It had sharding, it had caching layers, it had elaborate custom C++ backend servers
providing "collective intelligence" functionality, it had the whole works. 

And so when I started developing for them, they just sent me a virtual machine.
On the virtual machine was the entire development environment. It was a big 
download, but my setup time was almost nil. I just mounted the development
folder on the virtual machine to my dev box and started working on the files, 
and everything worked fine. They even provided a handy set of functions for
working with the virtual machine. 

This was great. Not only was the development environment under control, it
was really easy to work with. We didn't have any sort of problem with it until
a couple of months in where someone needed to change something in the environment.
Then - well, the ops team had to make the change to the core VM and redistribute
it to the whole team. It had to be them, they were the only ones who knew how
the VM fit together. 

Why do I mention this? Well, this job was the closest I'd ever seen to having 
a complicated deployment environment that was not just completely under control, 
but easy to work with. The only thing that I'd want to change - it would be nice
if the process for constructing the VM were a little more manageable. 

Vagrant & Chef
--------------
And that concludes story-time. Why did I need to tell you my back-story in exacting
detail? Well, vanity, mostly - (screen joke: I call it the Joel on Software ) - 
but I was also trying to illustrate where the value lies in Vagrant and Chef.

What is Vagrant
---------------
Vagrant is software that works in tandem with Oracle's free VirtualBox. 

You're probably familiar with VirtualBox or one of its analogues, like VMWare
Workstation - it's a virtual machine manager that allows you to create and 
destroy virtual machines on your computer. 

The thing is, VirtualBox requires that you actually install an entire operating
system from scratch. Starting from an Ubuntu ISO, it takes some time to get to
a working 'clean' operating system. It's also less trivial to completely automate
than I would have thought. Building an automatic run through the Ubuntu installation
process is kind of a pain in the ass - although it's quite likely to be easier
in more ops-centric distributions.

VirtualBox also provides command line tools for working with the operating systems
that you create, but they're complicated and fiddly.  (screen: VirtualBox's CLI manual) 

Vagrant is a toolkit that allows you to check out a virtual machine containing a 
clean installation of an operating system from a large library of potential
candidates, from the command line. It also provides simple command line tools
for starting, stopping, and connecting to the server that you create. 

What is Chef
------------
Once you have an empty virtual machine, you want to install your environment on it, 
preferrably in a way that is automated, repeatable, and if possible under-source control. 

This is where products like Chef and Puppet come in. They're configuration management
systems that allow for automated provisioning and configuration of systems, designed 
for use in big cloud deployments with dozens of computers that need to be kept in sync.

Some Differences Between Chef and Puppet, Why Chef?
---------------------------------------------------

Puppet and Chef are both solutions to the same problem.  They're competing products, 
and they are as alike as Coke and Pepsi. Anything you can do in one, you can do 
in the other without too much trouble. 

They both offer client-server options for managing dozens of servers at a time, they're
both well-documented and they're both open-source projects operated by companies hoping
that you'll fall in love with the product, then buy in to their expensive managed solution
to run it on the large scale. 

Puppet uses a Domain-specific Language, Chef uses a scaled-down Ruby. 

Puppet is declarative - it focuses on describing a desirable server state, whereas
Chef focuses on running scripts called 'recipes' to get you to a desirable server state.

Either of Chef or Puppet are a good choice for automatic provisioning. I'm just demonstrating
Chef because I know it a little better. 

Using Chef with Vagrant
-----------------------

Chef is designed to be used in a client-server configuration. One computer keeps track of the
configuration of dozens of computers who all run a chef client. 

With Vagrant, and a local development environment, we only have one computer that we're 
interested in configuring. The full-stack client and server set may be more software than 
we need. Fortunately, for this kind of small project, chef offers a scaled down version called
chef-solo, which we'll be using with Vagrant. 

A Sample Project
----------------

In order to get started with Chef and Vagrant, I'm going to concoct a small sample project, 
building a Node and Mongo environment. 

Let's get started. 

Install VirtualBox and Vagrant
------------------------------

Generally we would start by downloading and installing VirtualBox and Vagrant, but I'm going
to skip past that step for the sake of brevity. As well, I'm going to be doing a lot of command-line
work, here - on Windows, in the Git shell. 

These commands work the exact same say across Windows, Mac, and Linux. 

    mkdir sample_project
    cd vagrant_project

Create a Vagrant Project - vagrant init
---------------------------------------

We create a Vagrant project with the command

    vagrant init


Vagrantfile - Select a Box
--------------------------

We could immediately create a Vagrant environment by typing in 'vagrant up', but
before we do that, let's take a look at the Vagrantfile that vagrant has created 
for us. 

This line right here: 

    config.vm.box = "base" 

this is where we select which base virtual machine we're going to start with
when building this box. 

To look at a list of candidate virtual machines, visit

    http://vagrantbox.es

Let's use Ubuntu Precise Pangolin, 32-bit:

    http://files.vagrantup.com/precise32.box

We paste it into the Vagrantfile:
    
    config.vm.box = "precise32"
    config.vm.box_url = "http://files.vagrantup.com/precise32.box"

And now, when we create this virtual machine, it will download Ubuntu
Precise Pangolin from the above location. 

Vagrantfile - Opening Dev Ports
-------------------------------

Scrolling down a bit we get to the port forwarding line. Vagrant, by default, 
creates a virtual machine with NAT (Network Address Translation), so it can 
access the internet, but the internet can't access it. 

Through the Vagrantfile we can change the networking mode to "Bridged", which 
allows the virtual machine to connect to the public network, but I'd rather
just forward a dev port on localhost to the virtual machine. 

Let's forward 8080 on localhost to 8080 in the VM

    config.vm.network :forwarded_port, guest: 8080, host: 8080

Vagrantfile - Syncing a Dev Folder
----------------------------------

We also want to sync our code with the environment. It's nice to be able to 
edit the code on the external machine, where we have all of our favourite 
tools installed, but execute it on the internal machine, where we have
the carefully controlled execution environment. 
    
    config.vm.synced_folder ".", "/home/vagrant/synced" 


Chef - Installing Mongo and Node
--------------------------------

We want to have Mongo on our server. So, we need to enlist the help of chef-solo. 

First, we have to tell Vagrant where we're keeping our configuration files: 

    config.vm.provision :chef_solo do |chef|
        chef.cookbooks_path = "chef/cookbooks"
        chef.roles_path = "chef/roles"
        chef.data_bags_path = "chef/data_bags"
    end

Let's actually make those directories exist:

    mkdir chef
    mkdir chef/cookbooks
    mkdir chef/roles
    mkdir chef/data_bags

Then we are going to add the mongo cookbook. 

    https://github.com/edelight/chef-mongodb

Just download it, copy and paste it into the 'cookbooks' directory.

Reading the instructions, we find that this cookbook depends on the 'apt'
cookbook, so we're also going to download and install that. 

The apt cookbook is at:

    https://github.com/opscode-cookbooks/apt

And while we're at it, let's get the node.js cookbook: 

    https://github.com/mdxp/nodejs-cookbook

And its dependency, build-essential

    http://github.com/opscode-cookbooks/build-essential

Once again, we install these simply by downloading them and placing them
in the `chef/cookbooks` directory. 

Then, add the recipes 'apt', 'build-essential', 'mongodb::default', 
'nodejs', and 'nodejs::npm' to your Vagrantfile. 
    
    chef.add_recipe "apt"
    chef.add_recipe "mongodb::default"
    chef.add_recipe "nodejs"
    chef.add_recipe "nodejs::npm" 

And start up the server from the command line: 

    vagrant up

And what's this? An error? 

Debugging 
---------

    [2013-10-25T23:34:09+00:00] Fatal: Chef::Exceptions::Package package[mongodb-10gen] (mongodb::default line 22) had an error: Chef::Exceptions::Package:No version specified, and no candidate version available for mongodb-10gen
    Chef never successfully completed! Any errors should be visible in the output above. Please fix your recipes so that they properly complete. 

Thanks to the fact that Chef recipes are designed for use across many
different operating systems, they almost never work properly the first 
time you try them.

In this case, the mongodb recipe is looking for a mongodb-10gen package, where the
package in Ubuntu is just called 'mongodb'. We can change that by modifying the recipe, 
in `chef/cookbooks/mongodb/attributes/default.rb:83`

    default[:mongodb][:package_name] = "mongodb"

Now, we want to re-run the provisioner to make it work this time. 

    vagrant provision

It worked! Now we've a mongo and node environment to work with! 

Hello World
-----------
Create hello_world.js in the folder with your Vagrantfile 

    var http = require('http');
    http.createServer(function (req, res) {
      res.writeHead(200, {'Content-Type': 'text/plain'});
      res.end('Hello World\n');
    }).listen(8080, '0.0.0.0');
    console.log('Server running at http://127.0.0.1:8080/');

We're listening on 8080 - the same port we opened, earlier, and 0.0.0.0, which
listens on all IPs. 

Now we can log in to the server we've created
    vagrant ssh

Run the node server that we've created

    cd synced
    node hello_world.js

And connect to mongoDB

    mongo

You can even shut the server back off
    
    exit
    vagrant halt

Creating Your Own Cookbook 
--------------------------
We've been using pre-built cookbooks for Mongo and Node, but if you really
want to get your hands into the nitty-gritty of Chef, you're going to want
to build your own cookbooks. 

Cookbooks are composed of
* Recipes
* Attributes
* Templates
* Files
* Resources
* Providers
* Metadata 

### Recipes ### 

Recipes are the most important part of the cookbook - they are the actual script that 
performs the configuration. It's possible to have an entire cookbook that is nothing
but a recipe. 

The file in the 'recipes' folder named `default.rb` is what executes when you run the
cookbook. 

Here's an example of an extremely simple recipe for installing Python's "Flask" framework:

    package "python-pip"

    execute "pip install flask" do
        command "pip install flask"
        user "root" 
    end

### Attributes ###

Attributes are overridable default configuration variables for your program.
You might, for example, have a cookbook that could potentially install multiple versions
of a library - and you could then treat the version as an attribute. 

### Templates ###

Templates are .erb templates - it's common to configure apps like Apache - apps that keep
all of their individual configuration in large files - by creating a huge template of the
configuration file, filling it with variables, and then dropping it in to the correct
location on the target drive. 

### Files ###

Files are just important files that the cookbook may need. 

### Resources & Providers ###

On top of scripting functionality for themselves, recipes can also provide functionality
to other recipes. The 'apt' cookbook, for example, provides the 'add' and 'remove' 
resources to other recipes, allowing users to add and remove repositories from apt's 
sources. 

### Metadata ###

Finally, recipes often provide data about themselves - title, author, version and the like. 


Roles & Data-Bags
-----------------
To vastly over-simplify, think of a role as a run-list of recipes, and a data-bag as a 
database of configuration variables. 
