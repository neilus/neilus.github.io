---
layout: post
title: Testing Your Ansible Roles with Molecule
lang: en
ref: smart
categories: DevOps Ansible molecule testing tool framework
---

Introductioon
-------------

Disclaimer
==========

This is an introduction on how to test your Ansible roles. It is intended
for trained audience who are already familiar with Ansible. I won't cover
every feature of molecule in this document, just a few which I found the
most crucial to get started.

What is molecule?
=================

> Molecule is designed to aid in the development and testing of Ansible roles. Molecule provides support for testing with multiple instances, operating systems and distributions, virtualization providers, test frameworks and testing scenarios. Molecule is opinionated in order to encourage an approach that results in consistently developed roles that are well-written, easily understood and maintained.
-- [From the molecule documentation](https://molecule.readthedocs.io)

Since both Ansible and molecule are developed in python, they have the same
"pythonic" mindset. Therefore it comes handy - but not necessarry - to be a
bit familiar with some python tools for development and testing 
(like pip, virtualenvs or tox).

Installing Molecule
-------------------

As the most python tools [molecule can be installed](https://molecule.readthedocs.io/en/latest/installation.html)
via pip. Yes there are other methods to, especialy on linux with the major
package managers you can install some version on your system, but in my
experience if you want to treat your infrastructure as a code with a config
management or provisioner tool, then act like it and don't just install some
random software version your OS distributor or some repo provides and let it
loose and update whenever to whatever. The same is true to Ansible, and every
framework or library.

I recommend using virtualenv to isolate your development environments (yes,
you might want to consider using one venv per role/repo!), and so the method
is easy as 1-2-3:

1) [install the prerequesites](https://molecule.readthedocs.io/en/latest/installation.html#requirements)
 on your os, like python 2.7, virtualenv, pip, python development, openssl.
 You might want to consider installing Ansible later, via pip in the venv you
 will be working (minimal Ansible version supported is 2.2!).
 
2) Create the venv and install the packages

[![asciicast](https://asciinema.org/a/haFNq3iIE6z9Pj92pa6zDhUCk.png)](https://asciinema.org/a/haFNq3iIE6z9Pj92pa6zDhUCk){:target="_blank"}
<!--
<script src="https://asciinema.org/a/haFNq3iIE6z9Pj92pa6zDhUCk.js" id="asciicast-haFNq3iIE6z9Pj92pa6zDhUCk" async></script>
-->
    
3) You need to install the python packages for your desired "virtualization"
 provider (in this tutorial we'll stick to the default docker), but we'll come
 back to that later.
    
Now first of all, for convenience let's enable bash completion for molecule


    eval "$(_MOLECULE_COMPLETE=source molecule)"

Creating A Molecule-enabled Ansible Role
----------------------------------------

Now let's create a simple role which is molecule-enabled. We could use
`ansible-galaxy` for that, and just add our test scenarios, but molecule
also provides us a cli command to create a new role. So let's RTFM

[![asciicast](https://asciinema.org/a/6KDwn0r3PVGTdbvNcuOqHUbq2.png)](https://asciinema.org/a/6KDwn0r3PVGTdbvNcuOqHUbq2){:target="_blank"}
<!--
<script src="https://asciinema.org/a/6KDwn0r3PVGTdbvNcuOqHUbq2.js" id="asciicast-6KDwn0r3PVGTdbvNcuOqHUbq2" async></script>
-->

So now we have a pretty standard Ansible role, except the molecule directory,
which now contains a test scenario named default, since I left every option
on default, except the role name which is mandatory.

Now I'm so impatient, I want to start testing already!

[![asciicast](https://asciinema.org/a/C873l9R0yIVKUDwFg9x2bCaZY.png)](https://asciinema.org/a/C873l9R0yIVKUDwFg9x2bCaZY){:target="_blank"}
<!--
<script src="https://asciinema.org/a/C873l9R0yIVKUDwFg9x2bCaZY.js" id="asciicast-C873l9R0yIVKUDwFg9x2bCaZY" data-cols="90" async></script>
-->

Let's respect our elders, and do what it says, install the driver beforehand,
 in our case the docker driver for python:

[![asciicast](https://asciinema.org/a/pq2LNIcuUhf8HnWlzqgdX75sK.png)](https://asciinema.org/a/pq2LNIcuUhf8HnWlzqgdX75sK){:target="_blank"}
<!--
<script src="https://asciinema.org/a/pq2LNIcuUhf8HnWlzqgdX75sK.js" id="asciicast-pq2LNIcuUhf8HnWlzqgdX75sK" data-cols="90" async></script>
-->


Testing Against multiple Platforms
----------------------------------

Having Multiple Scenarios
-------------------------

Verifiers
---------

Testinfra
=========

Goss
====

Provisioner Configurations
--------------------------

Ansible Host/Group vars
=======================

Provisioner Playbooks
=====================

### Create and Destroy Playbooks
### Prepare and Provision Playbook
### Side Effect


