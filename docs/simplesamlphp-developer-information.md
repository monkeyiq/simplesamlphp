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




