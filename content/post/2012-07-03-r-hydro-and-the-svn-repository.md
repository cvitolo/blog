---
title: "R-Forge projects and the svn repository"
date: 2012-07-03 20:15:25+00:00
---

R projects available from R-Forge are version controlled using subversion (also known as svn). You can check out any repository using the "Anonymous Subversion Access", in terminal:

```
svn checkout svn://svn.r-forge.r-project.org/svnroot/projectname/
```

Each project may contain many packages (each in a separate folder).

To install a package in R, go to terminal and type:

```    
cd ~/projectname/pkg
R CMD check packagename
```

If there are no errors, you can install the package:

```
R CMD INSTALL packagename
```

To build the package tarball:

```
R CMD build packagename
```

If you make a change to the code and want to add your contribution to the repository, you should contact one of the administrators of the package to be added to the list of developers.

Once you have your own developer account you can commit your changes in this way:

  1. check out the last copy of the package using the Developer Subversion Access via SSH: `svn checkout svn+ssh://_developername_@svn.r-forge.r-project.org/svnroot/projectname/`
  2. go to the directory containing the project (e.g. `cd ~/projectname`)
  3. type `svn status` (you get a list of the files you have added (?) or deleted (!) in your copy)
  4. to apply the same changes to the repo, type: `svn add path/example.R` or `svn delete path/example.R`
  5. once you have done this or if you have simply modified existing file, then you are ready to commit: `svn commit`.
