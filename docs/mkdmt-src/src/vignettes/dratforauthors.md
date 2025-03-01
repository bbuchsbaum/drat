# Drat Basics for Package Authors: Distributing Packages

Dirk Eddelbuettel  
2015-05-24  

## Drat Overview

The [drat](https://dirk.eddelbuettel.com/code/drat.html) package makes it
trivially easy to deploy package repositories.  There are essentially just
two ways to use a package repository:

1) You _write_ to the repository as a _package author_ to publish your package; or
1) You _read_ from the reposiory as a _package user_ to install or update one or more packages.

This vignette deals with the first case: How to use
[drat](https://dirk.eddelbuettel.com/code/drat.html) as a package author.  A
[companion vignette for package users](/drat/vignettes/dratforusers) is available as well.


## Case 1: Using GitHub

### GitHub as a Web Server

The core motivation for [drat](https://dirk.eddelbuettel.com/code/drat.html)
comes from [GitHub](https://github.com/) and its implied web server.  As you
may know, _any_ GitHub user (or organization) can enable a website for a
given repository.  All it takes is to create either a
[git branch](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
named `gh-pages` (if following the original scheme), or creating a directory
`docs/` in the main branch.  After that, a website should be automatically
visible (though you should check under 'Settings').

To make this more explicit, consider a hypothetical user John with account
`johndoe`. Once John creates a repo `foo` and in it a branch `gh-pages` (or
an activated directory `docs/`), he will have a web address
`http://johndoe.github.io/foo` for this repo.

More formally, for a user `USER`, and a given repo named `drat`, we can
always assume `http://USER.github.io/drat`.

So for you as a package author with a given GitHub account, all that is
needed is a repository named `drat` with a `gh-pages` branch.  If you are
familiar with `git` at the command-line, you can just create the branch (and
the `src/contrib/` directory structure in it; see below).

If you are less familiar with git, a really easy shortcut is to simply fork the actual
[drat](https://dirk.eddelbuettel.com/code/drat.html) repo.  It contains the
drat source code which you could keep, or remove.  The fork only serves to
set up the required directory layout, and the `src/contrib/` directory.

### Place Files into the Local Repo

We can now assume that you have a local git repository named `drat` with a
subdirectory `src` containing a further subdirectory `contrib`.

You are now ready to insert a package into it.  For simplicit, let us assume
the package is named `myPkg` and is at version 0.5. So `R CMD build` created
a file `myPkg_0.5.tar.gz`.

Then via

```
## insert given package into default drat repo on local file system
drat::insertPackage("myPkg_0.5.tar.gz")
```

the source package will be copied into the default drat repo at location
`~/git/drat`. Should your git repository checkouts live in a different place
on your machine, just specify this either via the `options()` entry
"dratRepo" or directly:

```
## insert given package into given repo on local file system
drat::insertPackage("myPkg_0.5.tar.gz", "/srv/projects/git/drat")
```

In either case, the package will be copied into the repo, and the PACKAGES
file will be updated.

Lastly, if you have `git` (the command-line tool) or the wicked
[git2r](https://cran.r-project.org/package=git2r) package
installed, then you can also use the `commit=TRUE` option to have the new
files added and committed.  Neither of these variant pushes, so that last
step is left to the user (as it commonly requires authentication).

### GitHub and Travis Integration

[Colin Gillespie](https://github.com/csgillespie) has provided a nice
walk-through of how to have [Travis CI](https://travis-ci.org/) automagically
push packages into a [drat](https://dirk.eddelbuettel.com/code/drat.html)
repo. This is included as another (currently work-in-progress) vignette
entitled [Combining Drat And
Travis](/drat/vignettes/combiningdratandtravis)
which can be found in the [drat](https://dirk.eddelbuettel.com/code/drat.html)
package just like this vignette.

## Case 2: Using Another Server or Local Storage

Use of [drat](https://dirk.eddelbuettel.com/code/drat.html) is not limited to
GitHub.  Any server you can

+ write files to, and 
+ serve via http or file access from 

is suitable.  A common use case may be a local repository within a work group
or deparment, meant to be locally accessible but not from an outside network.

### Place Files into the Local Repo

This is similar to the usage described above. Suppose that you are part of
groupABC which has access to directory on shared filesystem somewhere, say
under `/nfs/groups/groupABC/` where you created a directory `drat` within a
directory `R`.  We once again require that the resulting directory
`/nfs/groups/groupABC/R/drat` contains a `src/contrib` directory structure.

Hence, the following command would copy the package and update the index files:

```
## insert given package into given repo on a network-local file system
drat::insertPackage("myPkg_0.5.tar.gz", "file://nfs/groups/groupABC/R/drat")
```

This updates the PACKAGES file (and its compressed variant) after which the
repository is ready to serve files. See
[the companion vignette](/drat/vignettes/dratforusers) for how to deploy it.
Note that the location URL should begin with `file:`.

## Summary

[drat](https://dirk.eddelbuettel.com/code/drat.html) permits package authors
to add packages very easily to R package repositories. These repositories can
be public, and GitHub provides a very natural option to serve a package
repository via the web server (based on either the `gh-pages` branch of a drat
or a `docs/` directory) to serve as GitHub Pages.

Repositories can also be local (and private) as well: all that
[drat](https://dirk.eddelbuettel.com/code/drat.html) requires to add packages
is write access to a directory.

Lastly, serving that directory as a repository then requires a web server
(easiest via the automatic GitHub repo option) or other file access.  How to
access packages from [drat](https://dirk.eddelbuettel.com/code/drat.html)
repository is described in [the companion vignette](/drat/vignettes/dratforusers).

