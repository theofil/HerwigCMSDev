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

## Analysis example starting from NANOAODSIM level
Supposing we want to compare ```Herwig matcbox``` samples with another generator, say with the ```amcatnloFXFX```, we first need to locate the files we want to use and copy some of them for easiness in some local/tmp folder in lxplus or elsewhere. For locating samples in DAS read the [https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookLocatingDataSamples](https://twiki.cern.ch/twiki/bin/view/CMSPublic/WorkBookLocatingDataSamples)

For example, here we will copy files from 

[DYJetsToLL_M-50_TuneCP5_13TeV-amcatnloFXFX-pythia8](https://cmsweb.cern.ch/das/request?instance=prod/global&input=file+dataset%3D%2FDYJetsToLL_M-50_TuneCP5_13TeV-amcatnloFXFX-pythia8%2FRunIISummer20UL18NanoAODv9-106X_upgrade2018_realistic_v16_L1v1-v2%2FNANOAODSIM) through ```xrdcp```.

We will supplement the logical filename of our interest with the prefix ```root://cms-xrd-global.cern.ch/```, for example for copying the

```
/store/mc/RunIISummer20UL18NanoAODv9/DYJetsToLL_M-50_TuneCP5_13TeV-amcatnloFXFX-pythia8/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v2/100000/13D0AD97-6B32-CB4C-BA87-5E37BA4CF20E.root
```
in a local ```lxplus``` folder we need to issue this command
```
xrdcp root://cms-xrd-global.cern.ch//store/mc/RunIISummer20UL18NanoAODv9/DYJetsToLL_M-50_TuneCP5_13TeV-amcatnloFXFX-pythia8/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v2/100000/13D0AD97-6B32-CB4C-BA87-5E37BA4CF20E.root .
```
Upon asking the file transfer, we will need to provide our grid certificate PEM password

```
cryptossl_X509CreateProxy: Your identity: /DC=ch/DC=cern/OU=Organic Units/OU=Users/CN=theofil/CN=650647/CN=Konstantinos Theofilatos
```

and of course having a valid grid certificate installed in our lxplus account, usually inside the hidden folder ```~/.globus```.

A useful tip is to use the ```dasgoclient``` to locate the files. *But*, don't forget first to create a valid ```voms-proxy-init```, for example the commands below


```
voms-proxy-init
dasgoclient --query "file dataset=/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/RunIISummer20UL18NanoAODv9-106X_upgrade2018_realistic_v16_L1v1-v4/NANOAODSIM"
```

returns the

```
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/50969F11-6274-3A45-B5AB-B1AACE38A467.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/91AD06CB-1DA8-0147-A07B-01DFDB07C1C1.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/5087FCA3-77D6-9C44-9B7F-A902DD25FC40.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/C2ECA792-3EB1-E248-81DD-CDC8F2F6AB30.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/641FD9B3-2228-C44C-B71A-A42ECEFCE62F.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/56C084D9-4056-0D40-890D-D86EF3F55E34.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/1F3C409E-391C-5E41-A23A-C1087837D0BF.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/89E36F8C-4DD0-C84F-B809-AC8B49ADED89.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/51474F11-98E5-9D41-AAA6-365C688DD2E6.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/8682CB20-50FA-F848-80F9-2768986D34EE.root
/store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/8AC7853F-DEFE-2340-863B-D0F0EEC03E19.root
```

We can try to copy all files recursively


```
xrdcp -r root://cms-xrd-global.cern.ch//store/mc/RunIISummer20UL18NanoAODv9/DYToLL_NLO_5FS_TuneCH3_13TeV_matchbox_herwig7/NANOAODSIM/106X_upgrade2018_realistic_v16_L1v1-v4/30000/ .
```

If our sample is *small* < 1-2 Gb, we can even try merge all our files ```*root``` in a single root file using hadd. In the example below a new file will be created that will include all files in a single file named as PPD-RunIISummer20UL18GEN-00015.root.

```
hadd PPD-RunIISummer20UL18GEN-00015.root *.root
```

Then download the [https://raw.githubusercontent.com/theofil/h7friends/main/makefriends.py](https://raw.githubusercontent.com/theofil/h7friends/main/makefriends.py) and use it to create end flat ROOT trees that could be used for quick physics analysis. Try to read and understand what the script is doing, it applies from cross-object cleaning and creates a weight that could be used to normalize by their cross sections ```(xs)``` different physics simulations.

```
wget https://raw.githubusercontent.com/theofil/h7friends/main/makefriends.py
python -i makefriends.py PPD-RunIISummer20UL18GEN-00015.root --xs 6048 --output h7mbox.root

### script's output below

TClass::Init:0: RuntimeWarning: no dictionary for class edm::Hash<1> is available
TClass::Init:0: RuntimeWarning: no dictionary for class edm::ProcessHistory is available
TClass::Init:0: RuntimeWarning: no dictionary for class edm::ProcessConfiguration is available
TClass::Init:0: RuntimeWarning: no dictionary for class edm::ParameterSetBlob is available
TClass::Init:0: RuntimeWarning: no dictionary for class pair<edm::Hash<1>,edm::ParameterSetBlob> is available
Getting sum of weights for all input files
PPD-RunIISummer20UL18GEN-00015.root with 150000 entries and sumW = 102196.7
sumWtot 102196   Ntot 150000
opening PPD-RunIISummer20UL18GEN-00015.root
event 0 of PPD-RunIISummer20UL18GEN-00015.root
event 10000 of PPD-RunIISummer20UL18GEN-00015.root
event 20000 of PPD-RunIISummer20UL18GEN-00015.root
event 30000 of PPD-RunIISummer20UL18GEN-00015.root
.
.
.
hSumW.Integral() 6048.0
number of events processed 150000
number of entries in the TTree 150000
number of sumW 6048.0
number of sumW2 566.8
```

In the end of the day you should have a small ROOT file named ```h7mbox.root``` that could be used for physics comparisons. You can find this file as well as other here [http://theofil.web.cern.ch/theofil/files/pcmbox/](http://theofil.web.cern.ch/theofil/files/pcmbox/) but it should be straightforward to replicate the procedure for any new ```NANOAODSIM``` sample that might appear in DAS.








