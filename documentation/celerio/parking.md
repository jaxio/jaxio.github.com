
copy paste zone...

### Source control system (optional)

Celerio leverages source control (SVN, CVS, GIT) usage to provide features allowing the user to take control over
certain generated file.

Before generating such file, Celerio always check if a file of the same name exists on disk and is present under
source control.

Celerio assumes that files under source control should not be overwritten arbitrarily.

To really take advantage of Celerio's collision features, you must use a source-control system for the project
you intent to generate with Celerio.
