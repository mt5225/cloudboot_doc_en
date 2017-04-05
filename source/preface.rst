***************
Preface
***************


Background
===========
Bare-metal provisioning is one of most import activities of IT operation, and usually it is done by hand, using OS DVD or USB. Given the variety of server vendors, as well as the  complexity of H/W and S/W configuration, bare-metal provisioning is considerred as a time-consuming, error-prone IT process performed only by high skill IT professionals. To meet the ever-higher demands for physical servers in enterprise, it is cirtical to automate OS provisioning in large scale.

Purpose
===========
Different server vendors have diffent ways to configure the physical server, and not compatible with each other. To automate the server H/W configureation and OS provisioning process, we propose unified standard and build a tool upon it, we call it *Cloudboot*.

This document describes the architecture, usage and API of Cloudboot.

Target Audience
===============
- IT professionals
- IT automation software developer and integrators 
- x86 server vendors

Contents of this document
==========================

.. csv-table::
    :header: Chapter, Content
    :widths: 5, 20

    Chapter One, "Archicture, modules and upgrade process"
    Chapter Two, Requirements and installation
    Chapter Three, "BootOS usage, development and upgrade"
    Chapter Four, H/W configuration specification and development guide
    Chapter Five, OS template
    Chapter Six, Virtual machine management
    Chapter Seven, Cloudboot API
    Appendix, Other useful informations

Github Repo
============
Cloudboot is opensouce, you may find Cloudboot source code at:

https://github.com/idcos/osinstall