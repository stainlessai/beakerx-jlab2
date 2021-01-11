# Guide to Building tip-of-tree BeakerX for Jupyterlab 2.2.9

This short guide outlines the procedure for creating a local environment that supports the following setup:

- BeakerX 2.0.x family components, bleeding edge version from various repositories
- Jupyterlab 2.2.9 (last lab 2 version, they're now on 3)
- Groovy kernel only (other kernels should be installable in a similar manner but they are omitted from this guide)

The code for this environment comes from multiple repositories and also a fork of table_display that will probably (at some point) be merged into master but this setup seems to work for now.

NOTE: If you are NOT building from scratch (i.e., you've initialized the submodules in this repo) you can skip to step #2

## 1. Checkout the required repositories
This guide assumes you are in some local directory `$BEAKERX_HOME` and you're using Conda at `$CONDA_HOME`

```
git clone https://github.com/twosigma/beakerx
git clone https://github.com/twosigma/eakerx_base
git clone https://github.com/twosigma/beakerx_kernel_base
git clone https://github.com/twosigma/beakerx_kernel_groovy
git clone https://github.com/twosigma/beakerx_widgets
git clone https://github.com/twosigma/beakerx_tests
git clone https://github.com/martinRenou/beakerx_tabledisplay.git
```

## 2. Patch the beakerx environment spec
```diff
diff --git a/beakerx-dist/configuration.yml b/beakerx-dist/configuration.yml
index bb9d8297..0a131d0e 100644
--- a/beakerx-dist/configuration.yml
+++ b/beakerx-dist/configuration.yml
@@ -1,4 +1,4 @@
-name: beakerx_all
+name: beakerx_j2
 channels:
   - conda-forge
 dependencies:
@@ -14,4 +14,4 @@ dependencies:
   - requests
   - pyspark
   - bottle
-  - jupyterlab=1
+  - jupyterlab=2
```

## 3. Create a local conda environment
```
conda env create -f beakerx/beakerx-dist/configuration.yml
conda activate beakerx_j2
```

## 4. Install base
```
cd beakerx_base
pip install -r requirements.txt
cd ..
```

## 5. Install kernel_base
```
cd beakerx_kernel_base
./gradlew install
cd ..
```

## 6. Install Groovy kernel
```
cd beakerx_kernel_groovy
./gradlew install
```

## 7. Install the beakerx_kernel_groovy tool
```
cd groovy-dist
pip install -r requirements.txt
cd ../..
```

## 8. Install the kernel into Jupyter
```
beakerx_kernel_groovy install
```

## 9. Install the local version of the widgets
```
cd beakerx_widgets/beakerx_widgets
pip install -e .
jupyter labextension install --no-build ../js 
```

N.B.: Using the `beakerx install --lab` command will install from NPM **NOT** from local 

## 10. Install the tabledisplay widget
```
cd beakerx_tabledisplay
git checkout lab_2
cd beakerx_tabledisplay 
pip install -r requirements.txt
jupyter labextension install --no-build ../js 
```
N.B.: Using the `beakerx install --lab` command will install from NPM **NOT** from local 

## 11. Build all lab components
```
jupyter lab build
```

# Validation & Testing
```
jupyter labextension list
```

Expected results:

```
JupyterLab v2.2.9
Known labextensions:
   app dir: $YOUR_CONDA_HOME/envs/beakerx_j2/share/jupyter/lab
        @beakerx/beakerx-tabledisplay v2.0.0  enabled  OK*
        @beakerx/beakerx-widgets v2.0.2  enabled  OK*
        @jupyter-widgets/jupyterlab-manager v2.0.0  enabled  OK

   local extensions:
        @beakerx/beakerx-tabledisplay: $BEAKERX_HOME/beakerx_tabledisplay/js
        @beakerx/beakerx-widgets: $BEAKERX_HOME/beakerx_widgets/js
```

