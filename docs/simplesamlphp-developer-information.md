# SimpleSAMLphp developer information

<!-- 
	This file is written in Markdown syntax. 
	For more information about how to use the Markdown syntax, read here:
	http://daringfireball.net/projects/markdown/syntax
-->

[TOC]

This document is intended to provide an overview of the code for developers.
It should be readable to new developers and developers who contribute as
time allows and may have forgotten some details over time.

## Overview

This is a living document and various sections and additions are being made
as my knowledge of the SSP code and environment expands. Hopefully this can help
others find their way around the code a bit quicker.

The `master` branch is where active development of the next release is
taking place. If you would like to contribute an update to and
existing release please checkout the branch for that release, for
example to make a contribution to the v2.1.1 release you would
checkout the [simplesamlphp-2.1
branch](https://github.com/simplesamlphp/simplesamlphp/tree/simplesamlphp-2.1).



## Build process

There are two main release targets for each release: slim and full.
This is done by the
[build-release.yml](https://github.com/simplesamlphp/simplesamlphp/blob/master/.github/workflows/build-release.yml)
script. The full version also contains some [simplesamlphp
modules](https://github.com/simplesamlphp/simplesamlphp/blob/master/.github/build/full.json).
The build will also include some files in the vendor directory in the 
full build that are not in the slim build.

If the SimpleSAMLphp code relies on other repositories on the [simplesamlphp](https://github.com/simplesamlphp) site then
they are brought in using composer as shown for example for the [saml2 library](https://github.com/simplesamlphp/simplesamlphp/blob/435ce92874a789101495504cd6c4da600fb01334/composer.json#L73).


## Documentation

The core of the simplesamlphp.org website is taken from
[simplesamlphp.github.io](https://github.com/simplesamlphp/simplesamlphp.github.io).
The [docs subdirectory](https://simplesamlphp.org/docs/) is built from
the [docs
subdirectory](https://github.com/simplesamlphp/simplesamlphp/tree/master/docs)
of the main repository on github.

## Following a simple login

The `SimpleSAML\Auth\Simple` class takes the authentication_source
name and can be used to find a login URL with `getLoginURL()`. The
getLoginURL method takes the return URL as it's only parameter. The
URL returned from `getLoginURL()` will be a request to module.php and
include the return URL information.

The module.php code `Module::process`. The `process` method uses
Symfony to dispatch the request. This may result in a call to
modules/core/src/Controller/Login.php in `Login::loginuserpass()`. The
code flows through `handleLogin()` which may call
`UserPassBase::handleLogin(authstateid, username, password)`. The
`handleLogin` method looks up the `$as = Auth\Source` and passes the
login credentials to the `$as->login( username, password )` method.

For an SQL based login this would be in
modules/sqlauth/src/Auth/Source/SQL.php and the `SQL::login` method.
This `login` method either returns the user attributes on success or
throws an exception on login failure.


