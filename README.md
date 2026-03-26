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
Repositório com o código de treino de ASR em nível de fonema para Voice Conversion (VC) e TTS (alinhamento texto-mel) usado em [StarGANv2-VC](https://github.com/yl4579/StarGANv2-VC) e [StyleTTS](https://github.com/yl4579/StyleTTS).

## Pré-requisitos
1. Python >= 3.7
2. Clonar o repositório:
```bash
git clone https://github.com/yl4579/AuxiliaryASR.git
cd AuxiliaryASR
```
3. Instalar dependências Python:
```bash
pip install SoundFile torchaudio torch jiwer pyyaml click matplotlib g2p_en librosa
```
4. Prepare seu dataset e coloque `train_list.txt` e `val_list.txt` na pasta `Data` (veja a seção de Treino para detalhes).

## Dispositivo (DirectML/CUDA/CPU)
- A seleção de dispositivo é automática via [Configs/config.yml](Configs/config.yml) (`device: auto`). A ordem de preferência é DirectML (AMD), depois CUDA e por fim CPU.
- Para GPUs AMD, instale `torch-directml` compatível com sua versão do Torch (ex.: `pip install torch-directml` usando torch 2.4.1). CUDA não é necessária com DirectML.
- Você pode forçar o dispositivo em [Configs/config.yml](Configs/config.yml): `device: directml` (DirectML), `device: cpu` (CPU) ou `device: cuda` (pede CUDA, mas cai para DirectML/CPU se não houver).
- Checagem rápida:
```bash
python - <<"PY"
import torch
from utils import get_device
device = get_device()
print(device)
print(torch.ones(2, device=device))
PY
```
Com DirectML ativo, o dispositivo mostrado será `privateuseone:0`.

## Treino
```bash
python train.py --config_path ./Configs/config.yml
```
No `config.yml`, indique os caminhos de treino/validação. O formato das listas é `filename.wav|label|speaker_number`. Veja [train_list.txt](https://github.com/yl4579/AuxiliaryASR/blob/main/Data/train_list.txt) como exemplo (subset do LJSpeech). Para ASR puro, `speaker_number` pode ser `0`; para TTS, atribuir IDs de falante pode ser útil.

Checkpoints e logs do TensorBoard vão para `log_dir`. Para acelerar o treino, aumente `batch_size` até onde couber na GPU. `batch_size = 64` consome cerca de 10 GB de VRAM.

### Idiomas
O repositório vem configurado para inglês com [g2p_en](https://github.com/Kyubyong/g2p), mas pode ser treinado em outros idiomas. Para isso, adapte [meldataset.py](https://github.com/yl4579/AuxiliaryASR/blob/main/meldataset.py#L86-L93) com seu fonemizador, ajuste o vocabulário em [word_index_dict.txt](https://github.com/yl4579/AuxiliaryASR/blob/main/word_index_dict.txt) e atualize `n_token` em `config.yml` para refletir o novo total de tokens. Uma opção é o [phonemizer](https://github.com/bootphon/phonemizer).

## Referências
- [NVIDIA/tacotron2](https://github.com/NVIDIA/tacotron2)
- [kan-bayashi/ParallelWaveGAN](https://github.com/kan-bayashi/ParallelWaveGAN)

## Agradecimentos
Agradecimentos ao [@tosaka-m](https://github.com/tosaka-m) pelo repositório de referência e discussões valiosas.
