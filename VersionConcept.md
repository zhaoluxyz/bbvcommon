Three different versions are used in the assemblies of bbv.Common
  * Assembly version
  * File version
  * Product version

**Assembly version** uses the pattern `{major}.{minor}.0.0`

**Product version** can contain additional information about the version such as _alpha_, _RC_

**File version** differs between version 6 and 7 of bbv.Common.

### V 6 ###

**File version** uses the pattern `{major}.{minor}.{svn_revision}.0`

**{major}** is the main version number shared by all components in a release. This number is incremented if there is a change that influenced most components in bbv.Common.

**{minor}** is the version of a single component or group of components. This number is incremented if a breaking change was introduced in a component of this assembly.

**{release}** is the svn revision number that tags the code in our svn repository from which the build was created. Greater numbers mean newer releases. This revision is determined per assembly. This way you can see whether an assembly was changed between two releases of bbv.Common. If the revision is still the same for an assembly then this assembly wasn't changed, it was just rebuilt.

The revision is not tracked in the assembly version to simplify integration in other libraries or frameworks. Otherwise, the dependent projects would have to be rebuilt on every change of a bbv.Common assembly.

### V 7 ###

**File version** uses the pattern `{major}.{minor}.{year_day}.{time}`

**{major}** is the main version number shared by all components in a release. This number is incremented if there is a change that influenced most components in bbv.Common.

**{minor}** is the version of a single component or group of components. This number is incremented if a breaking change was introduced in a component of this assembly.

**{year\_day}** is the year and day on which the assembly was built. The first two digits are the year - e.g 11 for 2011. The next 1 to 3 digits are the day of the year - e.g. 32 for the first of February.

**{time}** is the time the assembly was built - e.g 1445 for 14:45 / 2:45 pm


The date is not tracked in the assembly version to simplify integration in other libraries or frameworks. Otherwise, the dependent projects would have to be rebuilt on every change of a bbv.Common assembly.