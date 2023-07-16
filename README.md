# HerwigCMSDev
This page holds instructions on how to build and update the Herwig interface in CMSSW using your lxplus account, supplementary to what one can find in the official [CMS Herwig Twiki](https://twiki.cern.ch/twiki/bin/viewauth/CMS/Herwig7Interface), which I find harder to maintain.



## Which CMSSW release ?
Assuming that you want to commit something new, you first need to commit it to HEAD and later make a pull request (PR) to backport it to earlier releases (e.g., ultralegacy for Run 2 or others). Check which is the latestest greatest CMSSW release here: 

[https://twiki.cern.ch/twiki/bin/viewauth/CMS/ReleaseSchedule](https://twiki.cern.ch/twiki/bin/viewauth/CMS/ReleaseSchedule)

or using the famous **lxr**

[https://cmssdt.cern.ch/lxr/source](https://cmssdt.cern.ch/lxr/source)

Useful page for new developers:

   * [https://twiki.cern.ch/twiki/bin/view/CMS/SoftwareDevelopementToolsSubTask](https://twiki.cern.ch/twiki/bin/view/CMS/SoftwareDevelopementToolsSubTask)
   * [https://cms-sw.github.io/build-release.html
](https://cms-sw.github.io/build-release.html)
   
To find out which versions of CMSSW your account currently supports

`scram list CMSSW`

You might need set your account's enviromental variables to a different scram arch to see the latest greatest.

`ls  /cvmfs/cms.cern.ch/`

Will show you all the available `slc*_amd64_gcc***` architectures. Once you find out which is the one you want to use simply do 

`export SCRAM_ARCH=slc7_amd64_gcc11`

Assumming that you use BASH shell. Find out which linux shell you use by typing `echo $SHELL` in the command shell.

If you try to build CMSSW over an unsupported release, you will get a warning saying, e.g., 

> WARNING: Developer's area is created for non-production architecture slc7_amd64_gcc11. Production architecture for this release is el8_amd64_gcc11

or

> WARNING: Developer's area is created for architecture el8_amd64_gcc11 while your current OS is slc7_amd64.


In some cases, you might even need to login to a more recent lxplus*.cern.ch in 

## Fast Installation
### CMSSW_13_X_X
The latest greatest when writting this instructions was `CMSSW_13_X_X`

```
ssh theofil@lxplus8.cern.ch
mkdir mydir
cd mydir
export SCRAM_ARCH=el8_amd64_gcc11
cmsrel CMSSW_13_0_9
cd CMSSW_13_0_9/src/
cmsenv
git cms-addpkg GeneratorInterface/Herwig7Interface
scram b -j 8
```

Decide on the name of the branch that your developments will go and execute the

`git checkout -b myFavoriteDev`

in the command line.

### Ultralegacy CMSSW_10_6_22
```
export SCRAM_ARCH="slc7_amd64_gcc700" # setenv for csh
scram list CMSSW_10_6 # Gives list of available releases, most recent at time of writing is 10_6_22:
cmsrel CMSSW_10_6_22
cd CMSSW_10_6_22/src
cmsenv
git cms-addpkg GeneratorInterface/Herwig7Interface
scram b -j 8
```
