# PIP LIST

Package                 Version
----------------------- ---------------
absl-py                 2.4.0
audioread               3.1.0
certifi                 2026.2.25
cffi                    2.0.0
charset-normalizer      3.4.6
click                   8.3.1
colorama                0.4.6
contourpy               1.3.3
cycler                  0.12.1
decorator               5.2.1
Distance                0.1.3
filelock                3.25.2
fonttools               4.62.1
fsspec                  2026.2.0
g2p-en                  2.1.0
grpcio                  1.78.0
idna                    3.11
inflect                 7.5.0
Jinja2                  3.1.6
jiwer                   4.0.0
joblib                  1.5.3
kiwisolver              1.5.0
lazy-loader             0.5
librosa                 0.11.0
llvmlite                0.46.0
Markdown                3.10.2
MarkupSafe              3.0.3
matplotlib              3.10.8
more-itertools          10.8.0
mpmath                  1.3.0
msgpack                 1.1.2
networkx                3.6.1
nltk                    3.9.4
numba                   0.64.0
numpy                   2.4.3
packaging               26.0
pandas                  3.0.1
pillow                  12.1.1
pip                     26.0.1
platformdirs            4.9.4
pooch                   1.9.0
protobuf                7.34.1
pycparser               3.0
pyparsing               3.3.2
python-dateutil         2.9.0.post0
PyYAML                  6.0.3
RapidFuzz               3.14.3
regex                   2026.2.28
requests                2.33.0
scikit-learn            1.8.0
scipy                   1.17.1
setuptools              82.0.1
six                     1.17.0
soundfile               0.13.1
soxr                    1.0.0
sympy                   1.14.0
tensorboard             2.20.0
tensorboard-data-server 0.7.2
threadpoolctl           3.6.0
torch                   2.4.1
torch-directml          0.2.5.dev240914
torchaudio              2.4.1
torchvision             0.19.1
tqdm                    4.67.3
typeguard               4.5.1
typing_extensions       4.15.0
tzdata                  2025.3
urllib3                 2.6.3
Werkzeug                3.1.7



# AuxiliaryASR
This repo contains the training code for Phoneme-level ASR for Voice Conversion (VC) and TTS (Text-Mel Alignment) used in [StarGANv2-VC](https://github.com/yl4579/StarGANv2-VC) and [StyleTTS](https://github.com/yl4579/StyleTTS). 

## Pre-requisites
1. Python >= 3.7
2. Clone this repository:
```bash
git clone https://github.com/yl4579/AuxiliaryASR.git
cd AuxiliaryASR
```
3. Install python requirements: 
```bash
pip install SoundFile torchaudio torch jiwer pyyaml click matplotlib g2p_en librosa
```
4. Prepare your own dataset and put the `train_list.txt` and `val_list.txt` in the `Data` folder (see Training section for more details).

## Training
```bash
python train.py --config_path ./Configs/config.yml
```
Please specify the training and validation data in `config.yml` file. The data list format needs to be `filename.wav|label|speaker_number`, see [train_list.txt](https://github.com/yl4579/AuxiliaryASR/blob/main/Data/train_list.txt) as an example (a subset for LJSpeech). Note that `speaker_number` can just be `0` for ASR, but it is useful to set a meaningful number for TTS training (if you need to use this repo for StyleTTS). 

Checkpoints and Tensorboard logs will be saved at `log_dir`. To speed up training, you may want to make `batch_size` as large as your GPU RAM can take. However, please note that `batch_size = 64` will take around 10G GPU RAM. 

### Languages
This repo is set up for English with the [g2p_en](https://github.com/Kyubyong/g2p) package, but you can train it with other languages. If you would like to train for datasets in different languages, you will need to modify the [meldataset.py](https://github.com/yl4579/AuxiliaryASR/blob/main/meldataset.py#L86-L93) file (L86-93) with your own phonemizer. You also need to change the vocabulary file ([word_index_dict.txt](https://github.com/yl4579/AuxiliaryASR/blob/main/word_index_dict.txt)) and change `n_token` in `config.yml` to reflect the number of tokens. A recommended phonemizer for other languages is [phonemizer](https://github.com/bootphon/phonemizer).

## References
- [NVIDIA/tacotron2](https://github.com/NVIDIA/tacotron2)
- [kan-bayashi/ParallelWaveGAN](https://github.com/kan-bayashi/ParallelWaveGAN)

## Acknowledgement
The author would like to thank [@tosaka-m](https://github.com/tosaka-m) for his great repository and valuable discussions.
