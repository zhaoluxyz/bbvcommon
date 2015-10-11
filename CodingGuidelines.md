# Introduction #

To deliver external quality (features, performance, reliability) in every release, software needs good internal quality (readability, maintainability, extensibility, testability).

Therefore we use the following coding guidelines for all new code developed in bbv.Common. Note that these guidelines are updated when we learn new ways of developing software and that not all code is up to the current guidelines because of this.


All newly developed software must comply with...

# Automatic Tests #

All code has to be unit tested and has to provide specifications that can be checked automatically (acceptance tests with MSpec).

# StyleCop #

StyleCop is used to define the coding style. See the existing settings files for the details on the used rules.

Note that there exist different settings for different types of assemblies:
  * Production code - no special exceptions from default set of StyleCop rules
  * Unit test code - no documentation is enforced
  * Specifications code - special setting to comply with MSpec syntax

# xUnit #

xUnit is used for Unit Tests.

# MSpec #

MSpec is used for specifications (acceptance tests).

# Fluent Assertions #

Fluent Assertions.NET is used in both unit tests and specifications for assertions (no xUnit or MSpec assertions whenever possible).

# NuGet #

All packages that are available from NuGet have to be included using NuGet.

# Continuous Integration #

bbv.Common and bbv.Common.Contrib are built and released on TeamCity (its an internal server that can not be seen from the internet).


---


It is nice if a project has...

# Sample Project #

There may be sample application or sample unit test to see real world usage.