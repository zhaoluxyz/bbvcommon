## Bootstrapper reporting specifications ##
**when the bootstrapping process is reported**
  * should report names and descriptions of all extensions including    executables with behaviors attached to it and run and shutdown executors
## Bootstrapping specifications ##
**when the bootstrapper is run**
  * should only initialize contexts once for all extensions
  * should pass the initialized values from the contexts to the extensions
  * should execute the extensions and the extension points according to the strategy defined order
**when the bootstrapper is shutdown**
  * should only initialize contexts once for all extensions
  * should pass the initialized values from the contexts to the extensions
  * should execute the extensions and the extension points according to the strategy defined order
## Bootstrapping with behavior specifications ##
**when the bootstrapper is run with behavior attached**
  * should only initialize contexts once for all extensions
  * should pass the initialized values from the contexts to the extensions
  * should execute the extensions with its extension points and the behaviors according to the strategy defined order
**when the bootstrapper is shutdown with behavior attached**
  * should only initialize contexts once for all extensions
  * should pass the initialized values from the contexts to the extensions
  * should execute the extensions with its extension points and the behaviors according to the strategy defined order
## Bootstrapping with configuration section specifications ##
**when the bootstrapper is run with configuration section behavior and extension with customized loading**
  * should apply configuration section
  * should acquire section name
  * should acquire section
## Bootstrapping with extension configuration section specifications ##
**when the bootstrapper is run with extension configuration section behavior and extension with customized loading**
  * should use default conversion callback
  * should use conversion callbacks
  * should ignore not configured properties
  * should propagate configuration
  * should acquire section
