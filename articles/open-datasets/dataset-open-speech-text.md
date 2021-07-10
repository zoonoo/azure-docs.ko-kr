---
title: 러시아어 음성 텍스트로 열기
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 러시아어 Open Speech To Text 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 7dc004de8b4c01fca8eebf54332a9fa5c177ae13
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039055"
---
# <a name="russian-open-speech-to-text"></a>러시아어 음성 텍스트로 열기

다양한 오디오 원본에서 파생된 음성 샘플 컬렉션입니다. 이 데이터 세트는 러시아어로 된 짧은 오디오 클립을 포함합니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

이 러시아어 STT(Speech To Text) 데이터 세트에는 다음이 포함됩니다.
- ~1600만 발언
- ~20,000시간
- 2.3TB(int16의 .wav 형식으로 압축되지 않음), opus의 356GB
- 모든 파일이 opus로 변환됨(유효성 검사 데이터 세트 제외)

이 데이터 세트의 주요 목적은 음성 텍스트 변환 모델을 학습시키는 것입니다.

## <a name="dataset-composition"></a>데이터 세트 구성

.wav 파일에 대해 데이터 세트 크기가 제공됩니다.

| DATASET | UTTERANCES | HOURS | GB | SECS/CHARS | COMMENT | ANNOTATION | QUALITY/NOISE |
|-|-|-|-|-|-|-|-|
| radio_v4(*) | 7,603,192 | 10,430 | 1,195 | 5초/68 | 라디오 | 맞춤 | 95%/선명 |
| public_speech(*) | 1,700,060 | 2,709 | 301 | 6초/79 | 공개 연설 | 맞춤 | 95%/선명 |
| audiobook_2 | 1,149,404 | 1,511 | 162 | 5초/56 | 책 | 맞춤 | 95%/선명 |
| radio_2 | 651,645 | 1,439 | 154 | 8초/110 | 라디오 | 맞춤 | 95%/선명 |
| public_youtube1120 | 1,410,979 | 1,104 | 237 | 3초/34 | Youtube | 자막 | 95%/~선명 |
| public_youtube700 | 759,483 | 701 | 75 | 3초/43 | Youtube | 자막 | 95%/~선명 |
| tts_russian_addresses | 1,741,838 | 754 | 81 | 2초/20 | 주소 | TTS 4 음성 | 100%/선명 |
| asr_public_phone_calls_2 | 603,797 | 601 | 66 | 4초/37 | 전화 통화 | ASR | 70%/노이즈가 많음 |
| public_youtube1120_hq | 369,245 | 291 | 31 | 3초/37 | YouTube HQ | 자막 | 95%/~선명 |
| asr_public_phone_calls_1 | 233,868 | 211 | 23 | 3초/29 | 전화 통화 | ASR | 70%/노이즈가 많음 |
| radio_v4_add(*) | 92,679 | 157 | 18 | 6초/80 | 라디오 | 맞춤 | 95%/선명 |
| asr_public_stories_2 | 78,186 | 78 | 9 | 4초/43 | 책 | ASR | 80%/선명 |
| asr_public_stories_1 | 46,142 | 38 | 4 | 3초/30 | 책 | ASR | 80%/선명 |
| public_series_1 | 20,243 | 17 | 2 | 3초/38 | Youtube | 자막 | 95%/~선명 |
| asr_calls_2_val | 12,950 | 7\.7 | 2 | 2초/34 | 전화 통화 | 매뉴얼 주석 | 99%/선명 |
| public_lecture_1 | 6,803 | 6 | 1 | 3초/47 | 강의 | 자막 | 95%/선명 |
| buriy_audiobooks_2_val | 7,850 | 4\.9 | 1 | 2초/31 | 책 | 매뉴얼 주석 | 99%/선명 |
| public_youtube700_val | 7,311 | 4\.5 | 1 | 2초/35 | Youtube | 매뉴얼 주석 | 99%/선명 |

(*) 데이터 샘플만 txt 파일로 제공됩니다.

## <a name="annotation-methodology"></a>주석 방법론

이 데이터 세트는 오픈 소스를 사용하여 컴파일되었습니다. 긴 시퀀스는 음성 활동 감지 및 맞춤을 사용하여 오디오 청크로 분할되었습니다. 일부 오디오 유형은 자동으로 주석이 추가되었으며 통계적으로 휴리스틱을 사용하여 확인되었습니다.

## <a name="data-volumes-and-update-frequency"></a>데이터 볼륨 및 업데이트 빈도

데이터 세트의 총 크기는 350GB입니다. 공개적으로 공유되는 레이블이 포함된 이 데이터 세트의 총 크기는 130GB입니다.

이 데이터 세트 자체는 이전 버전과의 호환성을 위해 업데이트되지 않은 것 같습니다. 벤치마크의 경우 원본 리포지토리를 따르고 파일을 제외하세요.

나중에 새로운 도메인과 언어가 추가될 수 있습니다.

## <a name="audio-normalization"></a>오디오 정규화

모든 파일은 더 쉽고 빠른 런타임 확대를 위해 정규화됩니다. 다음과 같이 처리됩니다.

- 필요한 경우 모노로 변환됨
- 필요한 경우 16kHz 샘플링 비율로 변환됨
- 16비트 정수로 저장됨
- OPUS로 변환됨

## <a name="on-disk-db-methodology"></a>디스크 DB 방법론

각 오디오 파일(wav, 이진)은 해시됩니다. 해당 해시는 더 최적의 fs 작업을 위해 폴더 계층 구조를 만드는 데 사용됩니다.

```python
target_format = 'wav'
wavb = wav.tobytes()

f_hash = hashlib.sha1(wavb).hexdigest()

store_path = Path(root_folder,
                  f_hash[0],
                  f_hash[1:3],
                  f_hash[3:15] + '.' + target_format)
```

## <a name="downloads"></a>다운로드

이 데이터 세트는 다음과 같은 2가지 형식으로 제공됩니다.

- Azure Blob Storage 및/또는 직접 링크를 통해 사용할 수 있는 보관
- Azure Blob Storage를 통해 사용할 수 있는 원본 파일. 모든 파일은 https://azureopendatastorage.blob.core.windows.net/openstt/ 에 저장됨

폴더 구조:

```
└── ru_open_stt_opus                                            <= archived folders
│   │
│   ├── archives
│   │    ├── asr_calls_2_val.tar.gz                             <= tar.gz archives with opus and wav files
│   │    │   ...                                                <= see the below table for enumeration
│   │    └── tts_russian_addresses_rhvoice_4voices.tar.gz
│   │
│   └── manifests
│        ├── asr_calls_2_val.csv                                <= csv files with wav_path, text_path, duration (see notebooks)
│        │   ...
│        └── tts_russian_addresses_rhvoice_4voices.csv
│
└── ru_open_stt_opus_unpacked                                   <= a separate folder for each uploaded domain
    ├── public_youtube1120
    │    ├── 0                                                  <= see "On disk DB methodology" for details
    │    ├── 1
    │    │   ├── 00
    │    │   │  ...
    │    │   └── ff
    │    │        ├── *.opus                                   <= actual files
    │    │        └── *.txt
    │    │   ...
    │    └── f
    │
    ├── public_youtube1120_hq
    ├── public_youtube700_val
    ├── asr_calls_2_val
    ├── radio_2
    ├── private_buriy_audiobooks_2
    ├── asr_public_phone_calls_2
    ├── asr_public_stories_2
    ├── asr_public_stories_1
    ├── public_lecture_1
    ├── asr_public_phone_calls_1
    ├── public_series_1
    └── public_youtube700
```

| DATASET | GB, WAV | GB, ARCHIVE | 보관 | 원본 | MANIFEST |
|-|-|-|-|-|-|
| 학습 |  |  |  |  |  |
| 라디오 및 공개 연설 샘플 | - | 11.4 | opus+txt | - | manifest |
| audiobook_2 | 162 | 25.8 | opus+txt | 인터넷 + 맞춤 | manifest |
| radio_2 | 154 | 24.6 | opus+txt | 라디오 | manifest |
| public_youtube1120 | 237 | 19.0 | opus+txt | YouTube 비디오 | manifest |
| asr_public_phone_calls_2 | 66 | 9.4 | opus+txt | 인터넷 + ASR | manifest |
| public_youtube1120_hq | 31 | 4.9 | opus+txt | YouTube 비디오 | manifest |
| asr_public_stories_2 | 9 | 1.4 | opus+txt | 인터넷 + 맞춤 | manifest |
| tts_russian_addresses_rhvoice_4voices | 80.9 | 12.9 | opus+txt | TTS | manifest |
| public_youtube700 | 75.0 | 12.2 | opus+txt | YouTube 비디오 | manifest |
| asr_public_phone_calls_1 | 22.7 | 3.2 | opus+txt | 인터넷 + ASR | manifest |
| asr_public_stories_1 | 4.1 | 0.7 | opus+txt | 공개 스토리 | manifest |
| public_series_1 | 1.9 | 0.3 | opus+txt | 공개 시리즈 | manifest |
| public_lecture_1 | 0.7 | 0.1 | opus+txt | 인터넷 + 매뉴얼 | manifest |
| Val |  |  |  |  |  |
| asr_calls_2_val | 2 | 0.8 | wav+txt | 인터넷 | manifest |
| buriy_audiobooks_2_val | 1 | 0.5 | wav+txt | 도서 + 매뉴얼 | manifest |
| public_youtube700_val | 2 | 0.13 | wav+txt | YouTube 동영상 + 매뉴얼 | manifest |

## <a name="download-instructions"></a>다운로드 지침

### <a name="direct-download"></a>직접 다운로드

데이터 세트를 직접 다운로드하는 방법에 대한 지침은 [GitHub 다운로드 지침 페이지](https://github.com/snakers4/open_stt#download-instructions)를 참조하세요.

## <a name="additional-information"></a>추가 정보

데이터에 대한 도움이 필요하거나 질문이 있는 경우 aveysov@gmail.com의 데이터 작성자에게 문의하세요.

사용자는 이 라이선스를 통해 비상업적 용도로 그리고 만든 이에게 귀속된다는 조건에 한해서만 원하는 매체 또는 형식으로 소재를 배포, 리믹스, 조정 및 빌드할 수 있습니다. 다음과 같은 요소가 포함됩니다.
* BY – 크레딧은 만든 이에게 귀속됩니다.
* NC – 작품은 비상업적 사용만 허용됩니다.

CC-BY-NC 및 상업적 사용은 데이터 세트 작성자와 합의한 후 가능합니다.

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=open-speech-to-text)** .

## <a name="helper-functions--dependencies"></a>도우미 함수/종속성

### <a name="building-libsndfile"></a>libsndfile 빌드

상당한 오버헤드가 발생하지 않는 Python의 opus 파일을 읽는 효율적인 방법은 pysoundfile(libsoundfile 주변의 Python CFFI 래퍼)를 사용하는 것입니다.

Opus 지원이 업스트림으로 구현되었지만 제대로 릴리스되지 않았습니다. 따라서 사용자 지정 빌드 + 몽키패치를 옵트인(opt in)했습니다.

일반적으로 이 작업은 sudo 액세스 권한을 사용하여 셸에서 실행해야 합니다.

```bash
apt-get update
apt-get install cmake autoconf autogen automake build-essential libasound2-dev \
libflac-dev libogg-dev libtool libvorbis-dev libopus-dev pkg-config -y

cd /usr/local/lib
git clone https://github.com/erikd/libsndfile.git
cd libsndfile
git reset --hard 49b7d61
mkdir -p build && cd build

cmake .. -DBUILD_SHARED_LIBS=ON
make && make install
cmake --build .
```
### <a name="helper-functions--dependencies"></a>도우미 함수/종속성

다음 라이브러리를 설치합니다.

```
pandas
numpy
scipy
tqdm
soundfile
librosa
```

매니페스트는 다음 열이 포함된 csv 파일입니다.

- 오디오 경로
- 텍스트 파일 경로
- Duration

데이터에 액세스하는 가장 간단한 형식으로 입증되었습니다.

사용 편의성을 위해 모든 매니페스트는 다시 루트로 지정됩니다. 모든 경로는 상대적이므로 루트 폴더를 제공해야 합니다.

```python
# manifest utils
import os
import numpy as np
import pandas as pd
from tqdm import tqdm
from urllib.request import urlopen


def reroot_manifest(manifest_df,
                    source_path,
                    target_path):
    if source_path != '':
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: x.replace(source_path,
                                                                              target_path))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: x.replace(source_path,
                                                                                target_path))
    else:
        manifest_df.wav_path = manifest_df.wav_path.apply(lambda x: os.path.join(target_path, x))
        manifest_df.text_path = manifest_df.text_path.apply(lambda x: os.path.join(target_path, x))    
    return manifest_df


def save_manifest(manifest_df,
                  path,
                  domain=False):
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']

    manifest_df.reset_index(drop=True).sort_values(by='duration',
                                                   ascending=True).to_csv(path,
                                                                          sep=',',
                                                                          header=False,
                                                                          index=False)
    return True


def read_manifest(manifest_path,
                  domain=False):
    if domain:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration',
                               'domain'])
    else:
        return pd.read_csv(manifest_path,
                        names=['wav_path',
                               'text_path',
                               'duration'])


def check_files(manifest_df,
                domain=False):
    orig_len = len(manifest_df)
    if domain:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration']
    else:
        assert list(manifest_df.columns) == ['wav_path', 'text_path', 'duration', 'domain']
    wav_paths = list(manifest_df.wav_path.values)
    text_path = list(manifest_df.text_path.values)

    omitted_wavs = []
    omitted_txts = []

    for wav_path, text_path in zip(wav_paths, text_path):
        if not os.path.exists(wav_path):
            print('Dropping {}'.format(wav_path))
            omitted_wavs.append(wav_path)
        if not os.path.exists(text_path):
            print('Dropping {}'.format(text_path))
            omitted_txts.append(text_path)

    manifest_df = manifest_df[~manifest_df.wav_path.isin(omitted_wavs)]
    manifest_df = manifest_df[~manifest_df.text_path.isin(omitted_txts)]
    final_len = len(manifest_df)

    if final_len != orig_len:
        print('Removed {} lines'.format(orig_len-final_len))
    return manifest_df


def plain_merge_manifests(manifest_paths,
                          MIN_DURATION=0.1,
                          MAX_DURATION=100):

    manifest_df = pd.concat([read_manifest(_)
                             for _ in manifest_paths])
    manifest_df = check_files(manifest_df)

    manifest_df_fit = manifest_df[(manifest_df.duration>=MIN_DURATION) &
                                  (manifest_df.duration<=MAX_DURATION)]

    manifest_df_non_fit = manifest_df[(manifest_df.duration<MIN_DURATION) |
                                      (manifest_df.duration>MAX_DURATION)]

    print(f'Good hours: {manifest_df_fit.duration.sum() / 3600:.2f}')
    print(f'Bad hours: {manifest_df_non_fit.duration.sum() / 3600:.2f}')

    return manifest_df_fit


def save_txt_file(wav_path, text):
    txt_path = wav_path.replace('.wav','.txt')
    with open(txt_path, "w") as text_file:
        print(text, file=text_file)
    return txt_path


def read_txt_file(text_path):
    #with open(text_path, 'r') as file:
    response = urlopen(text_path)
    file = response.readlines()
    for i in range(len(file)):
        file[i] = file[i].decode('utf8')
    return file 

def create_manifest_from_df(df, domain=False):
    if domain:
        columns = ['wav_path', 'text_path', 'duration', 'domain']
    else:
        columns = ['wav_path', 'text_path', 'duration']
    manifest = df[columns]
    return manifest


def create_txt_files(manifest_df):
    assert 'text' in manifest_df.columns
    assert 'wav_path' in manifest_df.columns
    wav_paths, texts = list(manifest_df['wav_path'].values), list(manifest_df['text'].values)
    # not using multiprocessing for simplicity
    txt_paths = [save_txt_file(*_) for _ in tqdm(zip(wav_paths, texts), total=len(wav_paths))]
    manifest_df['text_path'] = txt_paths
    return manifest_df


def replace_encoded(text):
    text = text.lower()
    if '2' in text:
        text = list(text)
        _text = []
        for i,char in enumerate(text):
            if char=='2':
                try:
                    _text.extend([_text[-1]])
                except:
                    print(''.join(text))
            else:
                _text.extend([char])
        text = ''.join(_text)
    return text
```

```python
# reading opus files
import os
import soundfile as sf



# Fx for soundfile read/write functions
def fx_seek(self, frames, whence=os.SEEK_SET):
    self._check_if_closed()
    position = sf._snd.sf_seek(self._file, frames, whence)
    return position


def fx_get_format_from_filename(file, mode):
    format = ''
    file = getattr(file, 'name', file)
    try:
        format = os.path.splitext(file)[-1][1:]
        format = format.decode('utf-8', 'replace')
    except Exception:
        pass
    if format == 'opus':
        return 'OGG'
    if format.upper() not in sf._formats and 'r' not in mode:
        raise TypeError("No format specified and unable to get format from "
                        "file extension: {0!r}".format(file))
    return format


#sf._snd = sf._ffi.dlopen('/usr/local/lib/libsndfile/build/libsndfile.so.1.0.29')
sf._subtypes['OPUS'] = 0x0064
sf.SoundFile.seek = fx_seek
sf._get_format_from_filename = fx_get_format_from_filename


def read(file, **kwargs):
    return sf.read(file, **kwargs)


def write(file, data, samplerate, **kwargs):
    return sf.write(file, data, samplerate, **kwargs)
```

```python
# display utils
import gc
from IPython.display import HTML, Audio, display_html
pd.set_option('display.max_colwidth', 3000)
#Prepend_path is set to read directly from Azure. To read from local replace below string with path to the downloaded dataset files
prepend_path = 'https://azureopendatastorage.blob.core.windows.net/openstt/ru_open_stt_opus_unpacked/'


def audio_player(audio_path):
    return '<audio preload="none" controls="controls"><source src="{}" type="audio/wav"></audio>'.format(audio_path)

def display_manifest(manifest_df):
    display_df = manifest_df
    display_df['wav'] = [audio_player(prepend_path+path) for path in display_df.wav_path]
    display_df['txt'] = [read_txt_file(prepend_path+path) for path in tqdm(display_df.text_path)]
    audio_style = '<style>audio {height:44px;border:0;padding:0 20px 0px;margin:-10px -20px -20px;}</style>'
    display_df = display_df[['wav','txt', 'duration']]
    display(HTML(audio_style + display_df.to_html(escape=False)))
    del display_df
    gc.collect()
```

## <a name="play-with-a-dataset"></a>데이터 세트로 재생

### <a name="play-a-sample-of-files"></a>파일 샘플 재생

대부분의 플랫폼 브라우저는 네이티브 오디오 재생을 지원합니다. 따라서 HTML5 오디오 플레이어를 사용하여 데이터를 볼 수 있습니다.

```python
manifest_df = read_manifest(prepend_path +'/manifests/public_series_1.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')

```

```python
sample = manifest_df.sample(n=20)
display_manifest(sample)
```

### <a name="read-a-file"></a>파일 읽기

```python
!ls ru_open_stt_opus/manifests/*.csv
```

wav 및 opus 파일을 가장 잘 읽는 방법을 보여 주는 몇 가지 예입니다.

Scipy는 wav에 가장 빠르게 사용할 수 있습니다. Pysoundfile이 전반적으로 opus에 가장 적합합니다.

```python
%matplotlib inline

import librosa
from scipy.io import wavfile
from librosa import display as ldisplay
from matplotlib import pyplot as plt
```

#### <a name="read-a-wav"></a>wav 읽기

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_calls_2_val.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
from io import BytesIO

wav_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+wav_path)
data = response.read()
sr, wav = wavfile.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

#### <a name="read-opus"></a>opus 읽기

```python
manifest_df = read_manifest(prepend_path +'manifests/asr_public_phone_calls_2.csv')
#manifest_df = reroot_manifest(manifest_df,
                              #source_path='',
                              #target_path='../../../../../nvme/stt/data/ru_open_stt/')
```

```python
sample = manifest_df.sample(n=5)
display_manifest(sample)
```

```python
opus_path = sample.iloc[0].wav_path
response = urlopen(prepend_path+opus_path)
data = response.read()
wav, sr = sf.read(BytesIO(data))
wav.astype('float32')
absmax = np.max(np.abs(wav))
wav =  wav / absmax
```

```python
# shortest way to plot a spectrogram
D = librosa.amplitude_to_db(np.abs(librosa.stft(wav)), ref=np.max)
plt.figure(figsize=(12, 6))
ldisplay.specshow(D, y_axis='log')
plt.colorbar(format='%+2.0f dB')
plt.title('Log-frequency power spectrogram')
# shortest way to plot an envelope
plt.figure(figsize=(12, 6))
ldisplay.waveplot(wav, sr=sr, max_points=50000.0, x_axis='time', offset=0.0, max_sr=1000, ax=None)
```

<!-- nbend -->

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.