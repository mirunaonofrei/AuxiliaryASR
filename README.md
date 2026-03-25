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
