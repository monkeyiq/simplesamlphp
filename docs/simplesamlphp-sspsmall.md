# SimpleSAMLphp making a small installation

[TOC]

If you are looking to install SimpleSAMLphp for use either on a single
user development machine or a small network of around 20 users this is
the page for you. The process is documented on a few other pages but
there are things you will need to bring it all together which is what
this page is intended to provide. This page is also intended to be 
a somewhat gentle introduction to the process.

This is a work in process document.

## Introduction

SimpleSAMLphp allows sites to offer federated authentication of
hundreds of thousands of users. This might motivate you to want to use
SimpleSAMLphp for your next PHP web application so you can easily
expand to a large user base or have the app deployed in a university
or at a national research center level.

With all that said, everybody starts out trying to learn how the
pieces all fit together and it can be a little frustrating if you are
coming in without SAML or auth at large knowledge.

The really short story that I am working on sspsmall [SSP
Small](https://github.com/monkeyiq/sspsmall) which are a collection of
scripts to allow you to setup a SimpleSAMLphp installation and start
authenticating users against a local relational database.

This guide is here to expand on that so you have enough information to
know what the sspsmall scripts are doing and why.

## How does it all fit together?

An application is represented in SimpleSAMLphp as a service provider.
So if you are developing a php application called `SuperApp` you will
likely setup a `SuperApp` service provider in your SimpleSAMLphp
configuration.

Your application will know the URL of the SimpleSAMLphp installation and the
name of this service provider that you have configured. You then use the Simple
API in your app to ensure the user is authenticated.

```php
$as = new \SimpleSAML\Auth\Simple('SuperApp');
$as->requireAuth();
```

The actual authentication will be done by an Identity Provider (IdP).
You can setup the same SimpleSAMLphp to also provide and IdP. To
verify the user the IdP with use an authentication source (defined in
config/authsources.php) which might be a module to check a username
and password against data in a relational database.

So call from `requireAuth` above follows a process like that shown
below. Once the far right hand end is met either a failure to login
    (bad username, wrong password etc) or a success message will flow back
to the far left.


```
requireAuth -> SimpleSAMLphp SP -> SimpleSAMLphp IdP -> relational database
```

## Installation

The installation in sspsmall follows the main documentation installing
[the
tarball](simplesamlphp-install.html#download-and-install-simplesamlphp)
and [configuring
apache](simplesamlphp-install.html#configuring-apache) to serve that
install. The files ownership and protection are setup before
restarting apache to make the install available.

These steps are performed in `setup-as-root.sh` and `setup.sh` and do
not really require any knowledge of SAML or SimpleSAMLphp. Likewise
the final script `sync-as-root.sh` in sspsmall is just changing
permissions to make sure the installation runs smoothly.

The main SimpleSAMLphp is contained in the `sync.sh` script. There are
parts of the sync script, like the final part that creates an SQLite
test database that are fairly normal commands.

The start of the script copies over the main configuration files for
SimpleSAMLphp. The config.php is the core configuration file for
SimpleSAMLphp. You will need to change at least the URL that you can
access your SimpleSAMLphp with, the admin password, and a salt value
for cryptography. These are detailed in [SimpleSAMLphp configuration:
config.php](https://simplesamlphp.org/docs/2.3/simplesamlphp-install.html#simplesamlphp-configuration-configphp).

As well as that the sspsmall install sets up the metadata files for
running an SP and IdP in the one instance. The metadata files are
split into hosted and remote parts. For historic reasons, as of 2024,
a hosted SP is defined in config/authsources.php rather than
`metadata/saml20-sp-hosted.php`.

The config/authsources.php defines a `default-sp` with an entityID of
`https://sspsmall.example.org/` and wants that SP to use the IdP at
`urn:x-simplesamlphp:sspsmall-idp`.

That IdP identifier is used in both the metadata/saml20-idp-remote.php
and metadata/saml20-idp-hosted.php files. The entityID of the SP is
used in metadata/saml20-sp-remote.php to tell SimpleSAMLphp
information about the IdP (where it is) so authentication can
complete.

Because the SP and IdP are normally run on different hosts, possibly
in different physical locations and maybe run by different
organisations, there needs to be a way for the SP and IdP to be able
to trust each other. This is done with [server
keys](https://simplesamlphp.org/docs/2.3/simplesamlphp-idp.html#creating-a-self-signed-certificate)
which are generated using openssl, placed in the correct locations in
the file system with the right access modes to be read. The sspsmall
script automates this part of the process because you are installing
both on a single machine and might not care too much about it right at
the start.


