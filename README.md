# oct_sim
Quick simulation for the MMTP trigger
C++ version working, python version out-of-date

## SETUP AT SLAC

```bash
export ATLAS_LOCAL_ROOT_BASE=/cvmfs/atlas.cern.ch/repo/ATLASLocalRootBase
source ${ATLAS_LOCAL_ROOT_BASE}/user/atlasLocalSetup.sh
lsetup "root 6.20.06-x86_64-centos7-gcc8-opt" 
```

Note that the Makefile in this fork is slightly different from the original repo:

```diff
diff --git a/Makefile b/Makefile
index 4ba570e..3f9b596 100755
--- a/Makefile
+++ b/Makefile
@@ -2,10 +2,10 @@ ROOTCFLAGS    = $(shell root-config --cflags)
 ROOTGLIBS     = $(shell root-config --glibs)

 CXX            = g++
-CXXFLAGS       = -fPIC -Wall -O3 -g -std=c++11 -Wc++17-extensions
+CXXFLAGS       = -fPIC -Wall -O3 -g -std=c++11
 CXXFLAGS       += $(filter-out -stdlib=libc++ -pthread , $(ROOTCFLAGS))
 GLIBS          = $(filter-out -stdlib=libc++ -pthread , $(ROOTGLIBS))
-RPATH             = -rpath /Users/anthonybadea/builddir/lib
+RPATH             =

 INCLUDEDIR       = ./include/
 INCLUDEDIR              = $(PWD)/include/
@@ -26,7 +26,7 @@ DICT_FILES := $(wildcard include/*.pcm)

 MKDIR_BIN=mkdir -p $(PWD)/bin

-all: mkdirBin VectorDict sim testGBT sim_PCBEff_Modular validate_changes tpcosmics
+all: mkdirBin VectorDict sim testGBT sim_PCBEff_Modular validate_changes
 cosmics: mkdirBin VectorDict tpcosmics

 mkdirBin:
@@ -35,6 +35,7 @@ mkdirBin:
 VectorDict: $(INCLUDEDIR)VectorDict.hh
        rootcint -f $(INCLUDEDIR)VectorDict.cxx -c $(CXXFLAGS) -p $ $<
        touch $(INCLUDEDIR)VectorDict.cxx
+       cp $(INCLUDEDIR)VectorDict_rdict.pcm $(BINDIR)

 sim:  $(SRCDIR)sim.C $(OBJ_FILES) $(HH_FILES) $(DICT_FILES)
        $(CXX) $(CXXFLAGS) $(RPATH) -o $(BINDIR)sim $ $< $(GLIBS)
```

tpcosmics is not compiling with the setup above, so I removed it from the makefile (hopefully we won't need it). For some reason `VectorDict_rdict.pcm` was not being found, so I added a cp statement to the `VectorDict` makefile rule.


## PYTHON VERSION
WARNING: not up to date! If on herophysics, just to have numpy and pyroot together:
```{r, engine='bash', count_lines}
source hero_setup.sh
```
## C++ VERSION

```{r, engine='bash', count_lines}
make
./sim 
./sim -n 100 -ch <chamber type> -b <bkg rate in Hz/strip> -o output.root -uvr -thrx 3 -thruv 3
```
Configurable parameters include:
* [-n] nevents generated 
* [-sig] ART resolution (ns) :: default 32 ns
* [-x] road size :: default 8 strips
* [-w] BC window size :: default 8 BC
* [-ch] chamber type
* [-b] bkg rate in Hz/strip. Use -1 to get rate predicted at HL-LHC
* [-p] plotting :: default OFF
* [-o] output file :: 
* [-uvr] turn on UV roads :: default OFF
* [-hdir] name of histograms TDirectory :: default "histograms"
* [-e] chamber efficiency (0 to 1) :: default 0.9
* [-ideal-vmm] allow the VMM to always pick the signal hit :: default OFF
* [-ideal-tp] allow the TP to always pick the signal hit :: default OFF
* [-seed] set the seed for TRandom3 :: default none (seed is not set)
* [-thrx] set the X coincidence threshold :: default 2
* [-thruv] set the UV coincidence threshold :: default 2
* [-tree] write a TTree of hits and triggers :: default OFF
* [-angx] flat distribution of angles, in degrees :: default 0
* [-angy] flat distribution of angles, in degrees :: default 0
