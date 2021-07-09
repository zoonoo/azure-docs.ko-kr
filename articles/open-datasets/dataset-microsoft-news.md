---
title: Microsoft 뉴스 권장 사항 데이터 세트
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 Microsoft 뉴스 권장 사항 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: ecbad0a8c23bd2781373923ffa470a06509ee852
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038989"
---
# <a name="microsoft-news-recommendation"></a>Microsoft 뉴스 권장 사항

MIND(**MI** crosoft **N** ews **D** ataset, Microsoft 뉴스 데이터 세트)는 뉴스 권장 사항 연구를 위한 대규모 데이터 세트입니다. MIND는 Microsoft 뉴스 웹 사이트의 익명화된 동작 로그에서 수집되었습니다. MIND의 임무는 뉴스 추천의 벤치마크 데이터 세트 역할을 하고 뉴스 추천 및 추천 시스템 영역을 쉽게 연구할 수 있도록 하는 것입니다.

MIND에는 약 16만 개의 영문 뉴스 기사와 1백만 명의 사용자가 생성한 1,500만 개 이상의 광고 노출 로그가 포함되어 있습니다. 모든 뉴스 기사에는 제목, 요약, 본문, 범주, 엔터티를 비롯한 풍부한 텍스트 콘텐츠가 포함되어 있습니다. 각 광고 노출 로그에는 이 광고 노출 전 해당 사용자가 클릭한 이벤트, 클릭하지 않은 이벤트, 이전 뉴스 클릭 동작이 포함되어 있습니다. 사용자 개인 정보를 보호하기 위해 각 사용자는 익명화된 ID로 안전하게 해시될 때 프로덕션 시스템에서 연결이 끊어졌습니다. MIND 데이터 세트에 대한 자세한 내용은 [MIND: A Large-scale Dataset for News Recommendation](https://msnews.github.io/assets/doc/ACL2020_MIND.pdf)(MIND: 뉴스 추천을 위한 대규모 데이터 세트) 문서를 참조할 수 있습니다.
 
## <a name="volume"></a>볼륨

학습 데이터와 유효성 검사 데이터는 모두 zip으로 압축된 폴더이며, 해당 폴더는 다음과 같은 4개 파일을 포함합니다.

| 파일 이름 | 설명 |
|-|-|
| behaviors.tsv | 사용자의 클릭 기록 및 광고 노출 로그 |
| news.tsv | 뉴스 기사의 정보 |
| entity_embedding.vec | 지식 그래프에서 추출된 뉴스의 엔터티 포함 |
| relation_embedding.vec | 지식 그래프에서 추출된 엔터티 간 관계 포함 |

### <a name="behaviorstsv"></a>behaviors.tsv

behaviors.tsv 파일에는 광고 노출 로그와 사용자의 뉴스 클릭 기록이 포함되어 있습니다. 탭 기호로 구분된 다음과 같은 5개의 열을 포함합니다.

- Impression ID. 광고 노출 ID입니다.
- User ID. 사용자의 익명 ID입니다.
- 시간. “MM/DD/YYYY HH:MM:SS AM/PM” 형식의 광고 노출 시간입니다.
- 기록. 이 광고 노출 전 이 사용자의 뉴스 클릭 기록(클릭한 뉴스의 ID 목록)입니다.
- Impressions. 이 광고 노출에 표시된 뉴스와 해당 뉴스에 대한 사용자의 클릭 동작(클릭한 경우 1, 클릭하지 않은 경우 0)이 포함된 목록입니다.

아래 표에 예가 나와 있습니다.

| COLUMN | CONTENT |
|-|-|
| Impression ID | 123 |
| 사용자 ID | U131 |
| 시간 | 11/13/2019 8:36:57 AM |
| 기록 | N11 N21 N103 |
| Impressions | N4-1 N34-1 N156-0 N207-0 N198-0 |

### <a name="newstsv"></a>news.tsv

news.tsv 파일에는 behaviors.tsv 파일과 관련된 뉴스 기사의 세부 정보가 포함되어 있습니다. 탭 기호로 구분된 다음과 같은 7개의 열을 포함합니다.

- News ID
- 범주
- 하위 범주
- 제목
- 요약
- URL
- Title Entities(이 뉴스의 제목에 포함된 엔터티)
- Abstract Entities(이 뉴스의 요약에 포함된 엔터티)

MSN 뉴스 기사의 전체 콘텐츠 본문은 라이선스 구조로 인해 다운로드할 수 없습니다. 그러나 편의를 위해 데이터 세트의 MSN URL에서 뉴스 웹 페이지 구문을 분석하는 데 도움이 되도록 [유틸리티 스크립트](https://github.com/msnews/MIND/tree/master/crawler)를 제공합니다. 시간 제한으로 인해 일부 URL은 만료되어 액세스할 수 없습니다. 현재 이 문제를 해결하기 위해 최선의 노력을 다하고 있습니다.

다음 표에 예가 나와 있습니다.

| COLUMN | CONTENT |
|-|-|
| News ID | N37378 |
| 범주 | 스포츠 |
| SubCategory | 골프 |
| 제목 | PGA Tour 우승자 |
| 요약 | PGA Tour 최근 우승자의 갤러리입니다. |
| URL | https://www.msn.com/en-us/sports/golf/pga-tour-winners/ss-AAjnQjj?ocid=chopendata |
| Title Entities | [{“Label”: “PGA Tour”, “Type”: “O”, “WikidataId”: “Q910409”, “Confidence”: 1.0, “OccurrenceOffsets”: [0], “SurfaceForms”: [“PGA Tour”]}] |
| Abstract Entites | [{“Label”: “PGA Tour”, “Type”: “O”, “WikidataId”: “Q910409”, “Confidence”: 1.0, “OccurrenceOffsets”: [35], “SurfaceForms”: [“PGA Tour”]}] |

“Entities” 열의 사전 키에 대한 설명은 다음과 같습니다.

| 키 | 설명 |
|-|-|
| 레이블 | Wikidata 지식 그래프의 엔터티 이름 |
| 형식 | Wikidata의 이 엔터티 형식 |
| WikidataId | Wikidata의 엔터티 ID |
| 신뢰도 | 엔터티 링크 설정의 신뢰도 |
| OccurrenceOffsets | 제목 또는 요약 텍스트의 문자 수준 엔터티 오프셋 |
| SurfaceForms | 원본 텍스트의 원시 엔터티 이름 |

### <a name="entity_embeddingvec--relation_embeddingvec"></a>entity_embedding.vec 및 relation_embedding.vec

entity_embedding.vec 및 relation_embedding.vec 파일에는 TransE 메서드를 통해 WikiData 지식 그래프의 하위 그래프에서 학습한 엔터티와 관계의 100차원 포함이 들어 있습니다. 두 파일 모두에서 첫 번째 열은 엔터티/관계의 ID이며, 다른 열은 포함 벡터 값입니다. 이 데이터가 지식 인식 뉴스 추천 영역의 연구를 쉽게 하는 데 도움이 되기를 바랍니다. 예는 다음과 같습니다.

| ID | 포함 값 |
|-|-|
| Q42306013 | 0.014516 -0.106958 0.024590 … -0.080382 |

하위 그래프에서 포함을 학습할 때 몇 가지 이유로 인해 일부 엔터티는 entity_embedding.vec 파일에 포함이 없을 수 있습니다.

## <a name="storage-location"></a>스토리지 위치

데이터는 미국 서부/동부 데이터 센터에 있는 https://mind201910small.blob.core.windows.net/release/ Blob 컨테이너의 Blob에 저장됩니다.

컨테이너 내에서 학습 및 유효성 검사 세트는 각각 MINDlarge_train.zip 및 MINDlarge_dev.zip으로 압축됩니다.

## <a name="additional-information"></a>추가 정보

MIND 데이터 세트는 [Microsoft Research License Terms](https://github.com/msnews/MIND/blob/master/MSR%20License_Data.pdf)(Microsoft 연구 사용 조건)에 따라 연구 목적으로 자유롭게 다운로드할 수 있습니다. 데이터 세트에 대한 질문이 있는 경우 mind@microsoft.com에 문의하세요.

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset)** .

## <a name="demo-notebook-for-accessing-mind-data-on-azure"></a>Azure에서 MIND 데이터에 액세스하는 데모 Notebook

이 Notebook은 Azure의 Blob Storage에서 MIND 데이터에 액세스하는 예제를 제공합니다.

MIND 데이터는 미국 서부/동부 데이터 센터에 저장되므로 이 Notebook은 미국 서부/동부에 있는 Azure 컴퓨팅에서 보다 효율적으로 실행됩니다.

### <a name="imports-and-environment"></a>가져오기 및 환경

```python
import os
import tempfile
import shutil
import urllib
import zipfile
import pandas as pd

# Temporary folder for data we need during execution of this notebook (we'll clean up
# at the end, we promise)
temp_dir = os.path.join(tempfile.gettempdir(), 'mind')
os.makedirs(temp_dir, exist_ok=True)

# The dataset is split into training and validation set, each with a large and small version.
# The format of the four files are the same.
# For demonstration purpose, we will use small version validation set only.
base_url = 'https://mind201910small.blob.core.windows.net/release'
training_small_url = f'{base_url}/MINDsmall_train.zip'
validation_small_url = f'{base_url}/MINDsmall_dev.zip'
training_large_url = f'{base_url}/MINDlarge_train.zip'
validation_large_url = f'{base_url}/MINDlarge_dev.zip'
```

### <a name="functions"></a>Functions


```python
def download_url(url,
                 destination_filename=None,
                 progress_updater=None,
                 force_download=False,
                 verbose=True):
    """
    Download a URL to a temporary file
    """
    if not verbose:
        progress_updater = None
    # This is not intended to guarantee uniqueness, we just know it happens to guarantee
    # uniqueness for this application.
    if destination_filename is None:
        url_as_filename = url.replace('://', '_').replace('/', '_')
        destination_filename = \
            os.path.join(temp_dir,url_as_filename)
    if (not force_download) and (os.path.isfile(destination_filename)):
        if verbose:
            print('Bypassing download of already-downloaded file {}'.format(
                os.path.basename(url)))
        return destination_filename
    if verbose:
        print('Downloading file {} to {}'.format(os.path.basename(url),
                                                 destination_filename),
              end='')
    urllib.request.urlretrieve(url, destination_filename, progress_updater)
    assert (os.path.isfile(destination_filename))
    nBytes = os.path.getsize(destination_filename)
    if verbose:
        print('...done, {} bytes.'.format(nBytes))
    return destination_filename
```

### <a name="download-and-extract-the-files"></a>파일 다운로드 및 추출

```python
# For demonstration purpose, we will use small version validation set only.
# This file is about 30MB.
zip_path = download_url(validation_small_url, verbose=True)
with zipfile.ZipFile(zip_path, 'r') as zip_ref:
    zip_ref.extractall(temp_dir)

os.listdir(temp_dir)
```

### <a name="read-the-files-with-pandas"></a>pandas를 사용하여 파일 읽기

```python
# The behaviors.tsv file contains the impression logs and users' news click histories. 
# It has 5 columns divided by the tab symbol:
# - Impression ID. The ID of an impression.
# - User ID. The anonymous ID of a user.
# - Time. The impression time with format "MM/DD/YYYY HH:MM:SS AM/PM".
# - History. The news click history (ID list of clicked news) of this user before this impression.
# - Impressions. List of news displayed in this impression and user's click behaviors on them (1 for click and 0 for non-click).
behaviors_path = os.path.join(temp_dir, 'behaviors.tsv')
pd.read_table(
    behaviors_path,
    header=None,
    names=['impression_id', 'user_id', 'time', 'history', 'impressions'])
```

```python
# The news.tsv file contains the detailed information of news articles involved in the behaviors.tsv file.
# It has 7 columns, which are divided by the tab symbol:
# - News ID
# - Category
# - Subcategory
# - Title
# - Abstract
# - URL
# - Title Entities (entities contained in the title of this news)
# - Abstract Entities (entities contained in the abstract of this news)
news_path = os.path.join(temp_dir, 'news.tsv')
pd.read_table(news_path,
              header=None,
              names=[
                  'id', 'category', 'subcategory', 'title', 'abstract', 'url',
                  'title_entities', 'abstract_entities'
              ])
```

```python
# The entity_embedding.vec file contains the 100-dimensional embeddings
# of the entities learned from the subgraph by TransE method.
# The first column is the ID of entity, and the other columns are the embedding vector values.
entity_embedding_path = os.path.join(temp_dir, 'entity_embedding.vec')
entity_embedding = pd.read_table(entity_embedding_path, header=None)
entity_embedding['vector'] = entity_embedding.iloc[:, 1:101].values.tolist()
entity_embedding = entity_embedding[[0,
                                     'vector']].rename(columns={0: "entity"})
entity_embedding
```

```python
# The relation_embedding.vec file contains the 100-dimensional embeddings
# of the relations learned from the subgraph by TransE method.
# The first column is the ID of relation, and the other columns are the embedding vector values.
relation_embedding_path = os.path.join(temp_dir, 'relation_embedding.vec')
relation_embedding = pd.read_table(relation_embedding_path, header=None)
relation_embedding['vector'] = relation_embedding.iloc[:,
                                                       1:101].values.tolist()
relation_embedding = relation_embedding[[0, 'vector'
                                         ]].rename(columns={0: "relation"})
relation_embedding
```

### <a name="clean-up-temporary-files"></a>임시 파일 정리

```python
shutil.rmtree(temp_dir)
```

<!-- nbend -->

---

## <a name="examples"></a>예

Microsoft 뉴스 추천 데이터 세트를 사용하는 방법에 대한 다음 예제를 참조하세요.

- [MIND 뉴스 추천 과제](https://www.microsoft.com/research/?post_type=msr-academic-program&p=676692&secret=hCWU3V)
- [Microsoft 추천 리포지토리](https://github.com/microsoft/recommenders)
- [MIND: Microsoft 뉴스 데이터 세트](https://msnews.github.io/index.html)


## <a name="next-steps"></a>다음 단계

MIND에서 개발된 몇 가지 기본 뉴스 권장 사항 모델을 [Microsoft 추천 리포지토리](https://github.com/microsoft/recommenders)에서 확인하세요.


[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.