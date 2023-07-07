# Install environment
This assumes Linux enviroment is used (and `bash`):
```bash
mkdir /tmp/setup_conda_env
cd /tmp/setup_conda_env
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod 700 Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3
```
if you're using a supercomputer, replace $HOME above with your $WORK or $SCRATCH directory as appropriate. Your $HOME might not have enough space available. When running the Miniconda3 setup script, make sure you don't have any PYTHONPATH set. e.g. on supercomputers unload all the loaded Python modules with lmod.

This will install `conda` which is needed for future steps. Next you need to make sure your `.bashrc` sources conda paths by 
adding the following in that file:
```bash
source <your_conda_installation_path>/etc/profile.d/conda.sh
```
`your_conda_installation_path` is `$HOME/miniconda3` in case you are installing conda using this guide.
Next it's time to install `mamba` which is a environment package resolver that works much faster than `conda`:
```bash
conda install mamba -n base -c conda-forge
```
This will install `mamba` for your `base` environment.
Now let's create a new environment with packages that are required by `pyschism`, `ocsmesh` and other relevant useful tools such 
as `stormevents`:
```bash
mamba create -n simulation -c conda-forge jupyterlab ipython python==3.10 geos gdal proj shapely pyproj cartopy hdf5 netcdf4 udunits2 cfgrib cfunits appdirs 
```
If you want a more interactive environment add `ipython` and `jupyter-lab` to the above environment as well.
Note that if you don't have `git` in your environment (e.g. Anaconda PowerShell), you can install it in the environment by adding `git` to the list above.

Then we need to activate the newly created environemnt and start installing the rest of the packages using `pip`. 
For some of the packages we are using `git` to download the source code. If you don't have `git`, you can add that
to your environment as well. Note that you need to have `cmake` and `gcc` compilers for the next steps. **`gcc`** should be version **7** or higher and **`cmake`** should be version **3.9.4** or higher. On HPC platforms you can usually get those by using `module load gnu` or `module load gcc` and `module load cmake`.
```bash
conda activate simulation
pip install pyschism stormevents
git clone https://github.com/noaa-ocs-modeling/ocsmesh
cd ocsmesh
python setup.py install_jigsaw
pip install .
```
Note that it is also possible to install `ocsmesh` from PyPI and `jigsaw` and `jigsawpy` from `conda`, but as of now
the latest `jigsaw-python` available on `conda-forge` doesn't have fixes for some of the bugs I've noticed. In case you still prefer
to installed the packaged versions you can do the following instead of the previous block:
```bash
conda activate simulation
mamba install -c conda-forge jigsaw jigsawpy
pip install pyschism stormevents ocsmesh
```
Now the environment is ready for following along in the tutorials! Please let me know if you run into any issues
during these steps so that I can update the Gist accordingly

## A note on `pygeos`
At the time of this writing, OCSMesh is not using `shapely >= 2.x` and may still rely on `pygeos` for faster operations. However on some platforms due to incompatibilities that may arise between `pygeos` and `shapely` the tutorial sections may fail. Because of this, it is recommended that you **remove** `pygeos` at the end of installing everything in your environment (in case it's installed by other packages' requirements). To do so either use `pip uninstall pygeos` or `conda remove pygeos` in your environment. Note that `pygeos` might not be installed if you follow this updated installation guide.

## A note on `proj`
If your `proj` library is installed using `conda`, it is possible that conda doesn't set the right network variable for your `proj` when you activate the conda environment. This will sometimes result in some projection operations to fail (after waiting for a very long time!). To avoid this please set `PROJ_NETWORK` to `OFF` in your environment prior to using Python for OCSMesh or prior to running the Jupyter notebook. If you're using bash, you can do so by:
```bash
export PROJ_NETWORK="OFF"
```

# Download files needed for tutorial
In you work directory (current directory from which you'll run the 
Jupyter notebook) create a directory called `data` and download the
required DEMs as well as other files used throughout this tutorial.
Assuming you're going to run this notebook on a compute node that doesn't
have access to the internet, you need to run this on the login node:
```bash
# To create cartopy cache
cartopy_feature_download.py physical

cd /path/to/your/work/dir
mkdir data
cd data
```
```bash
wget https://www.nohrsc.noaa.gov/pub/staff/keicher/NWM_live/web/data_tools/NWM_channel_hydrofabric.tar.gz
tar -xf NWM_channel_hydrofabric.tar.gz

wget https://www.dropbox.com/s/t2e26p11ep0ydx1/shinnecock_inlet_test_case.zip
unzip shinnecock_inlet_test_case.zip fort.14

wget https://www.bodc.ac.uk/data/open_download/gebco/gebco_2022/geotiff/ -O gebco_2022.zip
unzip gebco_2022.zip gebco_2022_n90.0_s0.0_w-90.0_e0.0.tif

wget \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w074x25_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w074x00_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n40x75_w074x25_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n40x75_w074x00_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n40x75_w073x00_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w073x00_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w072x75_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w072x50_2015v1.tif \
    https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w072x25_2015v1.tif
```
**Note for Windows users**
If you're using PowerShell (e.g. Anaconda Powershell console), for wget operations you would need to specify an `-OutFile`. So it becomes something like
```powershell
wget https://www.nohrsc.noaa.gov/pub/staff/keicher/NWM_live/web/data_tools/NWM_channel_hydrofabric.tar.gz -OutFile NWM_channel_hydrofabric.tar.gz
tar -xf NWM_channel_hydrofabric.tar.gz

wget https://www.dropbox.com/s/t2e26p11ep0ydx1/shinnecock_inlet_test_case.zip -OutFile shinnecock_inlet_test_case.zip
unzip shinnecock_inlet_test_case.zip fort.14

wget https://www.bodc.ac.uk/data/open_download/gebco/gebco_2022/geotiff/ -OutFile gebco_2022.zip
unzip gebco_2022.zip gebco_2022_n90.0_s0.0_w-90.0_e0.0.tif

wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w074x25_2015v1.tif -OutFile ncei19_n41x00_w074x25_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w074x00_2015v1.tif -OutFile ncei19_n41x00_w074x00_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n40x75_w074x25_2015v1.tif -OutFile ncei19_n40x75_w074x25_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n40x75_w074x00_2015v1.tif -OutFile ncei19_n40x75_w074x00_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n40x75_w073x00_2015v1.tif -OutFile ncei19_n40x75_w073x00_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w073x00_2015v1.tif -OutFile ncei19_n41x00_w073x00_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w072x75_2015v1.tif -OutFile ncei19_n41x00_w072x75_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w072x50_2015v1.tif -OutFile ncei19_n41x00_w072x50_2015v1.tif
wget https://coast.noaa.gov/htdata/raster2/elevation/NCEI_ninth_Topobathy_2014_8483/northeast_sandy/ncei19_n41x00_w072x25_2015v1.tif -OutFile ncei19_n41x00_w072x25_2015v1.tif
```

# Update notebook for all paths and dataset versions
Please note that depending on when you get some of the files above, the data within might be of a different version than that of the tutorial notebook. For example for NWM, the notebook is using NWM v2.0, but at the time of this update to this readme, the current version downloadable is v2.1. Also for GEBCO dataset the version downloaded by the guide in this readme is 2022, while the one used in the tutorial is 2021.

Because of this when running the tutorial, you need to make sure you use the right version, path, etc. for the datasets when running on your machine (or HPC)
