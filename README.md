# AutoGluon on M1 Guide

This is a simple guide to get your M1 powered Mac to run AutoGluonTabular.

## Create or activate your environment
First create a virtualenv for testing(or activate and use any working python environment):

```shell
mkvirtualenv agtest
```
 
## Install LightGBM, XGBoost, and FastAI

Next install lightgbm and xgboost

```shell
pip install lightgbm xgboost fastai
```

## Install the non-tagged pre-release of CatBoost

Create a space for the binary:

```shell
mkdir /tmp/packages
cd /tmp/packages
git clone https://github.com/catboost/catboost.git 
```

Before building CatBoost you need to define 2 variables for your environment:

1. DPYTHON_CONFIG = path to python config file
2. DPYTHON_BIN = path to actual python binary file

Given that I used homebrew's python3.9, the values for me are:

1. `DPYTHON_CONFIG=/opt/homebrew/opt/python@3.9/bin/python3-config`
2. `DPYTHON_BIN=/opt/homebrew/opt/python@3.9/bin/python3.9`

Your values could be different, update accordingly. With these you can now start the build process:

```shell
cd catboost/catboost/python-package/catboost
../../../ya make -r -DUSE_ARCADIA_PYTHON=no -DOS_SDK=local -DPYTHON_CONFIG=/opt/homebrew/opt/python@3.9/bin/python3-config --target-platform=CLANG12-DARWIN-ARM64
python3 ../mk_wheel.py --target-platform=CLANG12-DARWIN-ARM64 -DPYTHON_BIN=/opt/homebrew/opt/python@3.9/bin/python3.9 -DPYTHON_CONFIG=/opt/homebrew/opt/python@3.9/bin/python3-config -DHAVE_CUDA=no --build-widget=no
```

Remember to update the variables called out above in those commands. 

Once the build has completed you can install with:

```shell
cd ..
pip install catboost-1.0.5-cp39-none-macosx_11_0_arm64.macosx_12_0_arm64.whl
```


## Install AutoGluon Tabular

Pretty easy here:

```shell
pip install --pre autogluon.tabular
```

## Reviewing Packages

Just as a sanity check, I've included the output from my fresh install, this step might provide working versions for dependencies if you find any issues.

```shell
pip freeze
```

```
autogluon.common==0.4.1b20220322
autogluon.core==0.4.1b20220322
autogluon.features==0.4.1b20220322
autogluon.tabular==0.4.1b20220322
blis==0.7.6
boto3==1.21.23
botocore==1.24.23
catalogue==2.0.7
catboost @ file:///private/tmp/packages/catboost/catboost/python-package/catboost-1.0.5-cp39-none-macosx_11_0_arm64.macosx_12_0_arm64.whl
certifi==2021.10.8
charset-normalizer==2.0.12
click==8.0.4
cloudpickle==2.0.0
cycler==0.11.0
cymem==2.0.6
dask==2021.11.2
distributed==2021.11.2
fastai==2.5.3
fastcore==1.3.29
fastdownload==0.0.5
fastprogress==1.0.2
fonttools==4.31.1
fsspec==2022.2.0
graphviz==0.19.1
HeapDict==1.0.1
idna==3.3
Jinja2==3.0.3
jmespath==1.0.0
joblib==1.1.0
kiwisolver==1.4.0
langcodes==3.3.0
lightgbm==3.3.2
locket==0.2.1
MarkupSafe==2.1.1
matplotlib==3.5.1
msgpack==1.0.3
murmurhash==1.0.6
networkx==2.7.1
numpy==1.22.3
packaging==21.3
pandas==1.3.5
partd==1.2.0
pathy==0.6.1
Pillow==9.0.1
plotly==5.6.0
preshed==3.0.6
psutil==5.8.0
pydantic==1.8.2
pyparsing==3.0.7
python-dateutil==2.8.2
pytz==2022.1
PyYAML==6.0
requests==2.27.1
s3transfer==0.5.2
scikit-learn==1.0.2
scipy==1.7.3
six==1.16.0
smart-open==5.2.1
sortedcontainers==2.4.0
spacy==3.2.3
spacy-legacy==3.0.9
spacy-loggers==1.0.1
srsly==2.4.2
tblib==1.7.0
tenacity==8.0.1
thinc==8.0.15
threadpoolctl==3.1.0
toolz==0.11.2
torch==1.10.2
torchvision==0.12.0
tornado==6.1
tqdm==4.63.0
typer==0.4.0
typing_extensions==4.1.1
urllib3==1.26.9
wasabi==0.9.0
xgboost==1.5.2
zict==2.1.0
``` 

## Validating the Install

At this point AutoGluon is installed on M1 with all the known dependencies at this time. You should be able to build the classification tutorial outlined at: https://auto.gluon.ai/stable/tutorials/tabular_prediction/tabular-quickstart.html

To make it a bit easier, I installed Jupyter Lab with:

```
pip install jupyterlab   
```

Then running the service, you can also open the notebook within this repo.

```
jupyter lab
```

## Credits

Thanks to everyone who worked tirelessly to get these dependencies in a state where they work. Bonus thanks to @MariaLavrovskaya who mapped out the build process for CatBoost!

