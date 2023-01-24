This assumes Linux enviroment is used (and `bash`):
```bash
mkdir /tmp/setup_conda_env
cd /tmp/setup_conda_env
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod 700 Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh -b -p $HOME/miniconda3
```
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
mamba create -n simulation -c conda-forge python=3.9 geos gdal proj shapely pygeos pyproj cartopy hdf5 netcdf4 udunits2 cfgrib cfunits 
```
If you want a more interactive environment add `ipython` and `jupyter-lab` to the above environment as well.

Then we need to activate the newly created environemnt and start installing the rest of the packages using `pip`. 
For some of the packages we are using `git` to download the source code. If you don't have `git`, you can add that
to your environment as well. Note that you need to have `cmake` and `gcc` compilers for the next steps
```bash
conda activate simulation
pip install pyschism # to install from PyPI repository
pip install stormevents
git clone https://github.com/noaa-ocs-modeling/ocsmesh
cd ocsmesh
python setup.py install_jigsaw
pip install .
```
Note that it is also possible to install `ocsmesh` from PyPI and `jigsaw` and `jigsaw-python` from `conda`, but as of now
the latest `jigsaw-python` available on `conda-forge` doesn't have fixes for some of the bugs I've noticed. In case you still prefer
to installed the packaged versions you can do the following instead of the previous block:
```bash
conda activate simulation
mamba install -c conda-forge jigsaw jigsaw-python
pip install pyschism
pip install stormevents
pip install ocsmesh
```
Now the environment is ready for following along in the tutorials! Please let me know if you run into any issues
during these steps so that I can update the Gist accordingly
