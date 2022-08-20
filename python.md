# Python

## Installing miniconda

Check for the latest version [here](https://docs.conda.io/en/latest/miniconda.html).

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

## Add additional channels, i.e. repos

```bash
conda config --add channels <url>
```

## Create and use requirements.txt

Including how to remove the environment.

```bash
conda list -e > requirements.txt
conda create -n py37 --file requirements.txt
conda activate py37
conda env remove -n py37
```
