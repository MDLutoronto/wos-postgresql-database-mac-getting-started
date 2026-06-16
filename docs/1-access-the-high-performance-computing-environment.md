---
title: Accessing the High Performance Computing Environment
parent: Getting Started with the Web of Science PostgreSQL Database - Mac
layout: default
nav_order: 1
---

### Accessing the High Performance Computing Environment

If working in high performance computing environment is new to you, we would recommend you attend [SciNet workshops](https://education.scinet.utoronto.ca/) to learn more, especially their Intro to SciNet & Triullium workshop (run periodically) or watch [a recording of a previous session](https://www.youtube.com/@scinethpcattheuniversityof8962).

  
But here are some steps to get your started:

1. You do not need to install any programs or clients to access the environment from a Mac. Access is via Terminal.
2. You will use an SSH key to connect. This requires some initial configuration, but once this is done it is both more secure and more convenient. If you have not already generated a key pair, instructions on how to do so can be found [here](https://mdl.library.utoronto.ca/technology/tutorials/generating-ssh-key-pairs-mac). More detailed instructions are also available on the [SciNet wiki](https://docs.computecanada.ca/wiki/Using_SSH_keys_in_Linux). Remember, you'll need create a key-pair on any systems you intend to connect with!
3. To login to the remote host, use this command in Terminal: `ssh -i .ssh/myprivatekeyname <computercanadausername>@trillium.scinet.utoronto.ca`. The system will prompt you to enter the passphrase for your key (Note, `-i .ssh/myprivatekeyname` is only necessary if you are not using the default key filepath and filename. See complete SSH setup instructions [here](https://mdl.library.utoronto.ca/technology/tutorials/generating-ssh-key-pairs-mac) for more information).
4. You are now connected to the server
5. To log out, type `exit` and press Enter. You are now back in your local environment.