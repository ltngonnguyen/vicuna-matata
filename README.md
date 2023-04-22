# vicuna-matata

#### An easy way to install Vicuna on Linux

---

This repo showcases a quick and easy way to install [Vicuna](https://github.com/lm-sys/FastChat) by lm-sys, based on LLaMA by Meta, one of the most powerful LLMs on the market right now. From my short time playing around with it, I find Vicuna to be comparable to ChatGPT in many respects, and is one of the better LLaMA flavours right now (in comparison to Stanford's Alcapa or Berkeley's Koala).

---

## Step 1: Install Anaconda (or miniconda or mamba or micromamba)
Go [here](https://anaconda.org/anaconda/conda) and follow the instructions for your OS. Alternatively, use miniconda or mamba/micromamba if you need for speed. Mamba is generally faster than conda in solving environments. 

## Step 2: Create a conda environment for Text Generation Web UI

```
conda create -n vicuna-matata python=3.10.9
conda activate vicuna-matata 
```

### Alternatively 

You can just use this little command to install everything and **skip to Step 4**:
```
conda create -n vicuna-matata pytorch torchvision torchaudio pytorch-cuda=11.7 cuda-toolkit -c 'nvidia/label/cuda-11.7.0' -c pytorch -c nvidia
conda activate vicuna-matata
```

Keep in mind this alternative command will download roughly 2GB of data, and conda can be quite hit and miss with their download speed. If it's so unbearably slow, you can try some suggestions [in this thread](https://github.com/pytorch/pytorch/issues/17023).  [sandeepgadhwal's command](https://github.com/pytorch/pytorch/issues/17023#issuecomment-1214770710) of `conda config --set add_anaconda_token False` helped me, your mileage might vary.

## Step 3: Install PyTorch and CUDA (skip this step if you used the alternative command)

```
pip3 install torch torchvision torchaudio
conda install -c conda-forge cudatoolkit-dev 
```

WARNING: Depending on your internet connection speed, the command `conda install -c conda-forge cudatoolkit-dev` can take a very long time to complete, without any progress bar of update. Mine took anywhere from 10 to 30 minutes, so be patient, do something else and come back later. 

## Step 4: Install the Text Generation Web UI and its dependencies

Credits to [oobabooga](https://github.com/oobabooga/text-generation-webui) for the amazing work with this front end. 

```
git clone https://github.com/oobabooga/text-generation-webui
cd text-generation-webui
pip install -r requirements.txt 
```

## Step 5: Download the weights from huggingface

The Vicuna team did not provide their own weights, only the instructions on how to convert the LLaMA weights (which requires a ton of computing resources), but luckily [anon8231489123](https://huggingface.co/anon8231489123/vicuna-13b-GPTQ-4bit-128g) came to the rescue and did all that hard work for us. Still, it's a file downloaded from the internet, so use at your own risk.  

```
python download-model.py anon8231489123/vicuna-13b-GPTQ-4bit-128g
```

## Step 6: Install GPTQ for LLaMa (oobabooga's fork)


```
mkdir repositories
cd repositories
git clone https://github.com/oobabooga/GPTQ-for-LLaMa.git -b cuda
cd GPTQ-for-LLaMa
pip install -r requirements.txt
python setup_cuda.py install
```

NOTE: `python setup_cuda.py install` will fail if you're using a GCC version higher than 11.5, so Google how to install multiple versions of GCC and then set the appropriate ENV variable. i.e. [Ubuntu](https://www.fosslinux.com/39386/how-to-install-multiple-versions-of-gcc-and-g-on-ubuntu-20-04.htm) . If you're on an Archlike bleeding edge rolling release distro (Arch, Artix, EndeavorOS, Manjaro, etc.), just install gcc9-bin from the AUR, then open up .bashrc or .zshrc or .profile and add `export CC=gcc-9 CXX=g++-9`, then `source .profile` (or .bashrc or .zshrc) 

## Step 7: Profit???

```
cd ..
cd ..
python server.py --model anon8231489123/vicuna-13b-GPTQ-4bit-128g --auto-devices --wbits 4 --groupsize 128
```

Now open up your favourite web browser, type in http://127.0.0.1:7860 (or simply http://localhost:7860). You might also need to turn off your adblocker for it to work. And that's it, you now have access to one of the most powerful LLMs on the market, right from the comfort and privacy of your own PC.  

The default interface will be an Instruct interface (similar to gpt3-davinci and the likes), if you want a chat interface (like ChatGPT), use `python server.py --model anon8231489123/vicuna-13b-GPTQ-4bit-128g --auto-devices --wbits 4 --groupsize 128 --chat` instead. 

Also, the model tends to hallucinate and start saying things like "Human: abcxyz , Assitant: abcxyz", you can fix it by going to Parameters tab -> Custom stopping strings -> Type in:

```
"### Human:", "Human:", "user:"
```

Or anything else that is at the beginning of the line that AI started its hallucination such as: "Translator:" or "English teacher:"
