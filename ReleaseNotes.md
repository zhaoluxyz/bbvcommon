# 7.0.12149.1635 #

## General ##
  * updated all reference libraries
  * Updated nuget command line tool

# 7.0.12089.2010 #

## General ##
  * updated all reference libraries
  * updated NuGet package infos

# 7.0.11331.1827 #

## General ##
  * updated all reference libraries
  * updated NuGet package infos

## Bootstrapper ##

Added [Bootstrapper](Bootstrapper.md).

## Event Broker ##

  * now all handlers pass exceptions to extensions and re-throw them only if no extension handled it

## Mapping Event Broker Extension ##
  * BREAKING CHANGE: Destination event args type provider receives source event args type. This allows convention based mappings


# 7.0.1187.616 #

## General ##
  * bbv.Common now available as NuGet packages
  * .Net 4.0 is now targeted
  * changed to Client Profile where possible
  * all log4net related code is extracted to dedicated assemblies
  * merged bbv.Common.Formatters and bbv.Common.Diagnostics into bbv.Common
  * removed HighResolutionStopWatch

## State Machine ##

  * checks now that there exists at most one transition per state and event that has no guard and that this transition is the last one to be checked when executing an event on the state machine
  * guards and transition actions can now be defined that have no or a single argument (instead ob object[.md](.md))
  * initial state is entered when state machine is started (not anymor when Initialize is called)

### Reports ###
  * yEd Report: you can now generate a .graphml file from your state machine definition that can be graphically layout with yEd
  * csv report: you can now generate a csv report from your state machine definition that can be loaded with OpenOffice, Excel, Google Spreadsheet, ...

## IO ##
  * added folder watcher to listen to individual file changes

## internals ##
  * moved from svn to git
  * code is now hosted on GitHub
  * teamcity.codebetter is now used as continuous integration server