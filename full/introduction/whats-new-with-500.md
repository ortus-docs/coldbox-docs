# What's New With 5.0.0

ColdBox 5.0.0 is a major release for the ColdBox MVC platform.  It has been long standing as we have been learning so much especially around containerization and portability.  This release has over 70 key issues addressed from new features, improvements and bug fixes.  So let's begin our ColdBox 5 adventure.

## Engine Deprecation

It is also yet another source code reduction due to the dropping of support for the following CFML Engines:

* Adobe ColdFusion 9
* Adobe ColdFusion 10

That's right, you will need Adobe ColdFusion 11+ or Lucee 4.5+ in order to work with ColdBox 5.

## Automation

We have fully automated all build processes with ColdBox 5 to include CommandBox and TestBox testing, Travis integration and a fully automated test suite that executes against **ALL** supported CFML engines.  Our code coverage has increased due to this work dramatically.  We discovered engine bugs that must have plagued our users for years.  YAY for testing!

## Performance Improvements

As we update core files we keep optimizing the source code and migrating to full cfscript.  This migration has allowed us to optimize very old code into modern times with significant performance gains.  We have also moved from internal Java reflection to get file information to native CFML functions since now all engines support it.  Just this alone has improved vanilla requests tremendously.

## Core Framework Exception Handling

The core framework has been revised with a fine tooth comb to provide better exception messages, better helpful messages and also the ability to intercept exceptions at the framework level via normal exception handlers.


