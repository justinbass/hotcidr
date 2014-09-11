Hot CIDR
========

Hot CIDR provides tools for firewall rule management and automation. The
toolchain currently supports AWS. Expansion to other popular infrastructures
is in the works.

Table of Contents
-----------------

 - [Workflows](#workflow)
 - [Setup](#setup)
 - [Documentation Notes](#documentation-notes)
 - [Authors](#authors)
 - [Copyright and license](#copyright-and-license)

Workflow
--------
### User wishes to update firewall rules
 - User clones firewall rules repository
 - User commits changes to local version
 - User submits commit as a pull request
 - Network administrator approves/merges

Setup
-----
### Toolchain installation
Optional: setup and activate python virtual environment

```
% virtualenv venv
% source venv/bin/activate
```

Clone repository and install

```
% git clone 'https://github.com/viasat/hotcidr'
% cd hotcidr/HotCIDR
HotCIDR% python setup.py install
```


### Jenkins CI Setup
- Install jenkins and configure
- Install and configure [ghprb plugin](https://git.viasat.com/jkwan/ghprb-fork#installation)
- Customize provided validate script for running the job


### Audit Webapp
See [README](audit-webapp/README.md)


### Repository setup
Fetch the VPC

```
% hc-fetch <vpc-region-code> <output-directory> --aws-access-key-id AWS_ACCESS_KEY_ID --aws-secret-access-key AWS_SECRET_ACCESS_KEY

e.g.
% hc-fetch us-west-2 ./us-west-2-core --aws-access-key-id AWS_ACCESS_KEY_ID --aws-secret-access-key AWS_SECRET_ACCESS_KEY

```

Setup as git repository

```
% cd fw_rules
fw_rules% git init
fw_rules% git add .
fw_rules% git commit -m "Initial commit"
fw_rules% git remote add origin YOUR_REPO_URL
fw_rules% git push -u origin master
```


### Automatic rule application (TODO)
Setup cronjob for hc-apply to run in background (script runs on interval in background)

1. Run `crontab -e` in unix shell to open up cronjob config file
   
2. Add following line to end of cronjob config file (15 signifies script will run every 15 minutes)
   
    SHELL=/bin/bash
    */15 * * * * cd ~/hotcidr/HotCIDR/bin/; hc-apply GITURL >> ~/hotcidr/HotCIDR/applyLog/log\_apply

This script will maintain consistency between GitRepository and AWS while logging output in "applyLog".

Documentation Notes
-------
### Apply
Apply will not update security-groups that are not associated with an instance, nor rules that are incorrectly formatted.

### Audit Webapp
Auditing can be done from the [auditing webapp](audit-webapp/README.md), or from the command line where HotCIDR is installed:

    hc-audit <repo>

The <repo> can be either a local git repository (a local directory) or a remote git repository (a git url, either https or ssh).

### Expirations

For testing or safety purposes, certain rules can be set to expire. For example, a rule allowing all inbound traffic with any port and protocol is probably meant for temporary testing, and can be tagged to expire after a reasonable time period.

There are two ways to cause a rule to expire:

1.  Add an 'expiration' field to any rule.

```
rules:
- direction: inbound
   protocol: all
   location: 0.0.0.0/0
   expiration: 86400
```

Authors
-------
This code was initially written by [Justin Bass](http://www.justinalanbass.com), James Kwan, and Austin Solomon.


Copyright and License
---------------------
Code and documentation copyright 2014 ViaSat, Inc. Code is released under [the Apache 2.0 license](LICENSE).
