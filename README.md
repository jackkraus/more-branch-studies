# more-branch-studies

To build Athena and run the scripts needed to run

For the `32k-basket`
```
git clone https://github.com/jackkraus/more-branch-studies.git;
mkdir build run; 
mv more-branch-studies/package_filters.txt .;
mv more-branch-studies/32k-basket/PoolWriteConfig.py .;
lsetup "asetup 25.0.8,Athena" ; 
git clone https://gitlab.cern.ch/akraus/athena.git; 
rm athena/Database/AthenaPOOL/AthenaPoolCnvSvc/python/PoolWriteConfig.py;
mv PoolWriteConfig.py athena/Database/AthenaPOOL/AthenaPoolCnvSvc/python/; 
cd athena; 
git remote add upstream https://:@gitlab.cern.ch:8443/atlas/athena.git ;
git fetch --all;
git checkout jacks-test-branch
cd ..;
```

For the `64k-basket`
```
git clone https://github.com/jackkraus/more-branch-studies.git;
mkdir build run; 
mv more-branch-studies/package_filters.txt .;
mv more-branch-studies/64k-basket/PoolWriteConfig.py .;
lsetup "asetup 25.0.8,Athena" ; 
git clone https://gitlab.cern.ch/akraus/athena.git; 
rm athena/Database/AthenaPOOL/AthenaPoolCnvSvc/python/PoolWriteConfig.py;
mv PoolWriteConfig.py athena/Database/AthenaPOOL/AthenaPoolCnvSvc/python/; 
cd athena; 
git remote add upstream https://:@gitlab.cern.ch:8443/atlas/athena.git ;
git fetch --all;
git checkout jacks-test-branch
cd ..;
```

then run the build scripts
``` 
cd build ;
lsetup 'asetup 25.0.8, Athena';
cmake -DATLAS_PACKAGE_FILTER_FILE=../package_filters.txt ../athena/Projects/WorkDir >& cmakelog;
make -j >& makelog;
```
don't forget to source:
```
source x86_64-el9-gcc13-opt/setup.sh;
```


**Navigate to a directory to run the following scripts:**

```
mkdir ../run && cd ../run;
```


Run the derivation script (more to come)
```
#retrieve inputs

rucio download mc23_13p6TeV:AOD.33799166._000303.pool.root.1
ln -fs mc23_13p6TeV/AOD.33799166._000303.pool.root.1 ./AOD.33799166._000303.pool.root.1
rucio download mc23_13p6TeV:AOD.33799166._000304.pool.root.1
ln -fs mc23_13p6TeV/AOD.33799166._000304.pool.root.1 ./AOD.33799166._000304.pool.root.1
rucio download mc23_13p6TeV:AOD.33799166._000305.pool.root.1
ln -fs mc23_13p6TeV/AOD.33799166._000305.pool.root.1 ./AOD.33799166._000305.pool.root.1
rucio download mc23_13p6TeV:AOD.33799166._000306.pool.root.1
ln -fs mc23_13p6TeV/AOD.33799166._000306.pool.root.1 ./AOD.33799166._000306.pool.root.1
rucio download mc23_13p6TeV:AOD.33799166._000307.pool.root.1
ln -fs mc23_13p6TeV/AOD.33799166._000307.pool.root.1 ./AOD.33799166._000307.pool.root.1
rucio download mc23_13p6TeV:AOD.33799166._000308.pool.root.1
ln -fs mc23_13p6TeV/AOD.33799166._000308.pool.root.1 ./AOD.33799166._000308.pool.root.1

#transform commands

# !!!! IMPORTANT !!!!
# Go to the respective Athena /build directory that you've created/want to test and source your build


export ATHENA_PROC_NUMBER=8
export ATHENA_CORE_NUMBER=8
Derivation_tf.py --inputAODFile="AOD.33799166._000303.pool.root.1,AOD.33799166._000304.pool.root.1,AOD.33799166._000305.pool.root.1,AOD.33799166._000306.pool.root.1,AOD.33799166._000307.pool.root.1,AOD.33799166._000308.pool.root.1" --athenaMPMergeTargetSize "DAOD_*:0" --multiprocess True --postExec "default:if ConfigFlags.Concurrency.NumProcs>0: cfg.getService(\"AthMpEvtLoopMgr\").ExecAtPreFork=[\"AthCondSeq\"];" --sharedWriter True --formats PHYS PHYSLITE --outputDAODFile 35010062._000389.pool.root.1 --multithreadedFileValidation True --AMITag p5855 --CA "all:True"
```

If the derivation script doesn't work you'll have to make sure you're sourcing the `build` directory correctly, Running `echo $PATH` should show you whether or it's coming from your `/build/` directory.