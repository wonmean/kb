# Python

## Venv
```bash
sudo apt -y install python3-venv \
&& python3 -m venv ~/venv/dev \
&& echo "alias py=\"source ~/venv/dev/bin/activate\"" | tee -a ~/.bashrc > /dev/null \
&& source ~/.bashrc
```

Activate and use pip to install dependencies.
```bash
py
```
```bash
pip install numpy
```

## A quick python3 http server
```bash
python3 -m http.server -d ~/tmp
```

## Install Miniconda
Check for the latest version [here](https://docs.conda.io/en/latest/miniconda.html).  
This does not need `sudo`.
```bash
wget --no-check-certificate https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
&& chmod 775 Miniconda3-latest-Linux-x86_64.sh \
&& ./Miniconda3-latest-Linux-x86_64.sh
```

## Other common commands
List and save the environment.
```bash
conda list -e > requirements.txt
```
Create a new environment from the `requirements.txt`.
```bash
conda create -n rnn --file requirements.txt
```
Remove an environment.
```bash
conda env remove -n rnn
```
Reset the base conda environment.
```bash
conda activate base \
&& conda install --rev 1
```
