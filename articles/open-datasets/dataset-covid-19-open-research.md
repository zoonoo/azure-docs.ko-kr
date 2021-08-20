---
title: 코로나19 공개 연구 데이터 세트
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 코로나19 공개 연구 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1225cc9f88fb07766f8f6e89e5a2502bdb83653a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039199"
---
# <a name="covid-19-open-research-dataset"></a>코로나19 공개 연구 데이터 세트

코로나19 및 코로나바이러스 관련 학술 문건의 전문 및 메타데이터의 데이터 세트가 컴퓨터 가독성을 위해 최적화되었으며 이 데이터 세트를 전 세계 연구계에서 사용할 수 있습니다.

코로나19 팬데믹에 대응하여 [Allen Institute for AI](https://allenai.org/)는 선도적인 연구 그룹과 협력하여 CORD-19(코로나19 공개 연구 데이터 세트)를 준비하여 배포했습니다. 이 데이터 세트는 47,000건 이상의 학술 문서로 구성된 무료 리소스이며, 여기에는 전 세계 연구계가 사용할 수 있는 코로나19 및 코로나바이러스군의 바이러스에 관한 36,000건 이상의 자료 전문이 포함되어 있습니다.

이 데이터 세트를 통해 동원된 연구자들은 전염병과의 사투를 지원하는 새로운 인사이트를 위해 자연어 처리에서 이룩한 최근의 발전을 적용합니다.

새로운 연구가 피어 리뷰 간행물 및 [bioRxiv](https://www.biorxiv.org/), [medRxiv](https://www.medrxiv.org/) 등의 기록 보관 서비스에 게시되면 자료가 업데이트될 수도 있습니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="license-terms"></a>사용 조건

이 데이터 세트는 Allen Institute of AI 및 [Semantic Scholar](https://pages.semanticscholar.org/coronavirus-research)에서 제공합니다. CORD-19 데이터 세트에서 제공되는 콘텐츠에 액세스하거나 콘텐츠를 다운로드하거나 사용하면 이 데이터 세트 사용과 관련된 [데이터 세트 라이선스](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/COVID.DATA.LIC.AGMT.pdf)에 동의하는 것으로 간주합니다. 데이터 세트의 개별 문건의 특정 라이선스 정보는 메타데이터 파일로 제공됩니다. 자세한 라이선스 정보는 [PMC 웹 사이트](https://www.ncbi.nlm.nih.gov/pmc/tools/openftlist/), [medRxiv 웹 사이트](https://www.medrxiv.org/submit-a-manuscript) 및 [bioRxiv 웹 사이트](https://www.biorxiv.org/about-biorxiv)에서 확인할 수 있습니다.

## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 JSON 포맷으로 저장되며 최신 릴리스에는 36,000여 건의 전문 자료가 포함되어 있습니다. 각 논문은 단일 JSON 개체로 표시됩니다. [스키마 보기](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)

## <a name="storage-location"></a>스토리지 위치
이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에서 컴퓨팅 리소스를 찾는 것이 좋습니다.

## <a name="citation"></a>인용

게시 또는 재배포에 CORD-19 데이터를 포함할 때는 데이터 세트에 대해 다음과 같이 인용하세요.

참고 문헌:

CORD-19(코로나19 공개 연구 데이터 세트). 2020. 버전 YYYY-MM-DD. [CORD-19(코로나19 공개 연구 데이터 세트)](https://pages.semanticscholar.org/coronavirus-research)에서 검색되었습니다. 액세스한 날짜 YYYY-MM-DD. doi:10.5281/zenodo.3715505

텍스트: (CORD-19, 2020)

## <a name="contact"></a>연락처

이 데이터 세트에 대한 질문이 있는 경우 partnerships@allenai.org으로 문의하세요.

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research)** .

## <a name="the-cord-19-dataset"></a>CORD-19 데이터 세트

CORD-19는 코로나19, SARS-CoV-2 및 관련 코로나 바이러스에 대한 40,000여 건의 전문 자료를 포함해 50,000여 건의 학술 문서로 구성된 문서 모음입니다. 이 데이터 세트는 코로나19 팬데믹에 맞서 싸우는 연구 커뮤니티를 돕기 위해 무료로 제공되었습니다.

이 Notebook의 목표는 두 가지입니다.

1. Azure에서 CORD-19 데이터 세트에 액세스하는 방법 시연: CORD-19 데이터 세트가 있는 Azure Blob Storage 계정에 연결합니다.
2. 데이터 세트의 구조 살펴보기: 데이터 세트의 아티클은 JSON 파일로 저장됩니다. 다음을 보여주는 예제를 제공합니다.

  - 아티클을 찾는 방법(컨테이너 탐색)
  - 아티클을 읽는 방법(JSON 스키마 탐색)

종속성: 이 Notebook에는 다음 라이브러리가 필요합니다.

- Azure Storage(예: `pip install azure-storage`)
- NLTK([문서](https://www.nltk.org/install.html))
- Pandas(예: `pip install pandas`)

### <a name="getting-the-cord-19-data-from-azure"></a>Azure에서 CORD-19 데이터 얻기

CORD-19 데이터가 Azure Open Datasets로 [여기](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)에 업로드되었습니다. 이 CORD-19 개방형 데이터 세트에 연결된 Blob service를 만듭니다.


```python
from azure.storage.blob import BlockBlobService

# storage account details
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = "sv=2019-02-02&ss=bfqt&srt=sco&sp=rlcup&se=2025-04-14T00:21:16Z&st=2020-04-13T16:21:16Z&spr=https&sig=JgwLYbdGruHxRYTpr5dxfJqobKbhGap8WUtKFadcivQ%3D"

# create a blob service
blob_service = BlockBlobService(
    account_name=azure_storage_account_name,
    sas_token=azure_storage_sas_token,
)
```

이 Blob service를 데이터에 대한 핸들로 사용할 수 있습니다. `BlockBlobService` API를 사용하는 데이터 세트를 탐색할 수 있습니다. 자세한 내용은 여기를 참조하세요.

* [Blob service 개념](/rest/api/storageservices/blob-service-concepts)
* [컨테이너에 대한 작업](/rest/api/storageservices/operations-on-containers)

CORD-19 데이터는 `covid19temp` 컨테이너에 저장됩니다. 이는 예제 파일이 함께 포함된 컨테이너 내의 파일 구조입니다.

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

각 .json 파일은 데이터 세트의 개별 아티클에 해당합니다. 여기서 제목, 작성자, 초록 및 전체 텍스트 데이터(사용 가능한 경우)가 저장됩니다.

### <a name="using-metadatacsv"></a>metadata.csv 사용
CORD-19 데이터 세트는 CORD-19 데이터 세트에서 사용할 수 있는 모든 논문에 관한 기본 정보를 기록하는 단일 파일인 `metadata.csv`와 함께 제공됩니다. 이 위치는 탐색을 시작하기에 적합합니다.


```python
# container housing CORD-19 data
container_name = "covid19temp"

# download metadata.csv
metadata_filename = 'metadata.csv'
blob_service.get_blob_to_path(
    container_name=container_name,
    blob_name=metadata_filename,
    file_path=metadata_filename
)
```

```python
import pandas as pd

# read metadata.csv into a dataframe
metadata_filename = 'metadata.csv'
metadata = pd.read_csv(metadata_filename)
```

```python
metadata.head(3)
```

한눈에 많은 것을 파악해야 하기 때문에 좀 더 다듬어 보겠습니다.

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```

```
There are 51078 many entries in this dataset:
-- 42511 have full text entries
-- 47741 have DOIs
-- 41082 have PubMed Central (PMC) ids
-- 964 have Microsoft Academic paper ids
```

## <a name="example-read-full-text"></a>예제: 전체 텍스트 읽기

`metadata.csv`에는 전체 텍스트 자체가 포함되지 않습니다. 이를 읽는 방법의 예를 살펴보겠습니다. 전체 텍스트 JSON 파일을 찾아서 압축을 풀고 문장 목록으로 변환합니다.

```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pdf_json/{1}.json'.format(example_entry['full_text_file'], example_entry['sha'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)
```

이제 다음과 같이 이 BLOB에 연결된 JSON 콘텐츠를 읽을 수 있습니다.

```python
import json
blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

이 예제의 목적을 위해 우리는 다음과 같이 텍스트 데이터를 저장하는 `body_text`에 관심이 있습니다.

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

전체 JSON 스키마는 [여기](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)에서 사용할 수 있습니다.


```python
from nltk.tokenize import sent_tokenize

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```    

### <a name="pdf-vs-pmc-xml-parse"></a>PDF 및 PMC XML 구문 분석

위의 예제에서는 `has_pdf_parse == True`의 사례를 살펴보았습니다. 이 경우 BLOB 파일 경로는 다음과 같은 형식입니다.

```
'<full_text_file>/pdf_json/<sha>.json'
```

또는 `has_pmc_xml_parse == True`의 경우 다음 형식을 사용합니다.

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

예를 들면 다음과 같습니다.


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)

blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
```
Full text blob for this entry:
custom_license/pmc_json/PMC546170.xml.json
An example sentence: Double-stranded small interfering RNA (siRNA) molecules have drawn much attention since it was unambiguously shown that they mediate potent gene knock-down in a variety of mammalian cells (1).
```    

## <a name="iterate-through-blobs-directly"></a>BLOB을 통해 직접 반복

위의 예제에서는 `metadata.csv` 파일을 사용하여 데이터를 탐색하고 BLOB 파일 경로를 구성하며 BLOB에서 데이터를 읽었습니다. 또 다른 방법은 BLOB 자체를 통해 반복하는 것입니다.


```python
# get and sort list of available blobs
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

이제 BLOB를 통해 직접 반복할 수 있습니다. 예를 들어 사용 가능한 JSON 파일 수를 세 보겠습니다.


```python
# we can now iterate directly though the blobs
count = 0
for blob in sorted_blobs:
    if blob.name[-5:] == ".json":
        count += 1
print("There are {} many json files".format(count))
```

```
There are 59784 many json files
```  

## <a name="appendix"></a>부록

### <a name="data-quality-issues"></a>데이터 품질 문제

이것은 명백한 이유로 다소 성급하게 조합된 대규모 데이터 세트입니다. 관찰된 몇 가지 데이터 품질 문제는 다음과 같습니다.

#### <a name="multiple-shas"></a>다수의 SHA

경우에 따라 지정된 항목에 대해 다수의 SHA가 있는 것이 관찰됩니다.

```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```
```
There are 1999 many entries with multiple shas
```

## <a name="layout-of-the-container"></a>컨테이너의 레이아웃

여기서는 간단한 regex를 사용하여 나중에 업데이트되는 경우를 위해 컨테이너의 파일 구조를 탐색합니다.

```python
container_name = "covid19temp"
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

```python
import re
dirs = {}

pattern = '([\w]+)\/([\w]+)\/([\w.]+).json'
for blob in sorted_blobs:
    
    m = re.match(pattern, blob.name)
    
    if m:
        dir_ = m[1] + '/' + m[2]
        
        if dir_ in dirs:
            dirs[dir_] += 1
        else:
            dirs[dir_] = 1
        
dirs
```

<!-- nbend -->

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research)** .

## <a name="the-cord-19-dataset"></a>CORD-19 데이터 세트

CORD-19는 코로나19, SARS-CoV-2 및 관련 코로나 바이러스에 대한 40,000여 건의 전문 자료를 포함해 50,000여 건의 학술 문서로 구성된 문서 모음입니다. 이 데이터 세트는 코로나19 팬데믹에 맞서 싸우는 연구 커뮤니티를 돕기 위해 무료로 제공되었습니다.

이 Notebook의 목표는 두 가지입니다.

1. Azure에서 CORD-19 데이터 세트에 액세스하는 방법 시연: AzureML 데이터 세트를 사용하여 CORD-19 데이터에 대한 컨텍스트를 제공합니다.
2. 데이터 세트의 구조 살펴보기: 데이터 세트의 아티클은 JSON 파일로 저장됩니다. 다음을 보여주는 예제를 제공합니다.

  - 아티클을 찾는 방법(디렉터리 구조 탐색)
  - 아티클을 읽는 방법(JSON 스키마 탐색)

종속성: 이 Notebook에는 다음 라이브러리가 필요합니다.

- AzureML Python SDK(예: `pip install --upgrade azureml-sdk`)
- Pandas(예: `pip install pandas`)
- NLTK([문서](https://www.nltk.org/install.html))(예: `pip install nltk`)

NLTK에 `punkt` 패키지가 없는 경우 다음을 실행해야 합니다.

```
import nltk
nltk.download('punkt')
```

### <a name="getting-the-cord-19-data-from-azure"></a>Azure에서 CORD-19 데이터 얻기

CORD-19 데이터가 Azure Open Datasets로 [여기](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)에 업로드되었습니다. 이 Notebook에서는 AzureML [데이터 세트](/python/api/azureml-core/azureml.core.dataset.dataset)를 사용하여 CORD-19 개방형 데이터 세트를 참조합니다.

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
cord19_dataset = Dataset.File.from_files('https://azureopendatastorage.blob.core.windows.net/covid19temp')
mount = cord19_dataset.mount()
```

`mount()` 메서드는 데이터 세트에 의해 정의된 파일 시스템 스트림을 로컬 파일로 탑재하는 컨텍스트 관리자를 만듭니다.

`mount.start()`와 `mount.stop()`을 사용하거나 그 대신 `with mount():`를 사용하여 컨텍스트를 관리합니다.

Mount는 Unix 또는 Unix 유사 운영 체제에서만 지원되며 libfuse가 있어야 합니다. Docker 컨테이너 내에서 실행 중인 경우 Docker 컨테이너는 `--privileged` 플래그로 시작하거나 `--cap-add SYS_ADMIN --device /dev/fuse`로 시작해야 합니다. 자세한 내용은 [문서](/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py#mount-mount-point-none----kwargs-&preserve-view=true)를 참조하세요.


```python
import os

COVID_DIR = '/covid19temp'
path = mount.mount_point + COVID_DIR

with mount:
    print(os.listdir(path))
```
```
['antiviral_with_properties_compressed.sdf', 'biorxiv_medrxiv', 'biorxiv_medrxiv_compressed.tar.gz', 'comm_use_subset', 'comm_use_subset_compressed.tar.gz', 'custom_license', 'custom_license_compressed.tar.gz', 'metadata.csv', 'noncomm_use_subset', 'noncomm_use_subset_compressed.tar.gz']
```

이는 예제 파일이 함께 포함된 CORD-19 데이터 세트 내의 파일 구조입니다.

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

각 .json 파일은 데이터 세트의 개별 아티클에 해당합니다. 여기서 제목, 작성자, 초록 및 전체 텍스트 데이터(사용 가능한 경우)가 저장됩니다.

## <a name="using-metadatacsv"></a>metadata.csv 사용

CORD-19 데이터 세트는 CORD-19 데이터 세트에서 사용할 수 있는 모든 논문에 관한 기본 정보를 기록하는 단일 파일인 `metadata.csv`와 함께 제공됩니다. 이 위치는 탐색을 시작하기에 적합합니다.


```python
import pandas as pd

# create mount context
mount.start()

# specify path to metadata.csv
COVID_DIR = 'covid19temp'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, COVID_DIR, 'metadata.csv')

# read metadata
metadata = pd.read_csv(metadata_filename)
metadata.head(3)
```

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```    

### <a name="example-read-full-text"></a>예제: 전체 텍스트 읽기

`metadata.csv`에는 전체 텍스트 자체가 포함되지 않습니다. 이를 읽는 방법의 예를 살펴보겠습니다. 전체 텍스트 JSON 파일을 찾아서 압축을 풀고 문장 목록으로 변환합니다.


```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
filepath = '{0}/{1}/pdf_json/{2}.json'.format(path, example_entry['full_text_file'], example_entry['sha'])
print("Full text filepath:")
print(filepath)
```    

이제 다음과 같이 이 파일에 연결된 JSON 콘텐츠를 읽을 수 있습니다.

```python
import json

try:
    with open(filepath, 'r') as f:
        data = json.load(f)
except FileNotFoundError as e:
    # in case the mount context has been closed
    mount.start()
    with open(filepath, 'r') as f:
        data = json.load(f)
        
# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

```
Keys within data: paper_id, metadata, abstract, body_text, bib_entries, ref_entries, back_matter
```

이 예제의 목적을 위해 우리는 다음과 같이 텍스트 데이터를 저장하는 `body_text`에 관심이 있습니다.

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

[전체 JSON 스키마](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)를 봅니다.


```python
from nltk.tokenize import sent_tokenize
# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
    

#### <a name="pdf-vs-pmc-xml-parse"></a>PDF 및 PMC XML 구문 분석

위의 예제에서는 `has_pdf_parse == True`의 사례를 살펴보았습니다. 이 경우 파일 경로는 다음과 같은 형식입니다.

```
'<full_text_file>/pdf_json/<sha>.json'
```

또는 `has_pmc_xml_parse == True`의 경우 다음 형식을 사용합니다.

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

예를 들면 다음과 같습니다.


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
filename = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Path to file: {}\n".format(filename))

with open(mount.mount_point + '/' + COVID_DIR + '/' + filename, 'r') as f:
    data = json.load(f)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```

## <a name="appendix"></a>부록

### <a name="data-quality-issues"></a>데이터 품질 문제

이것은 명백한 이유로 다소 성급하게 조합된 대규모 데이터 세트입니다. 관찰된 몇 가지 데이터 품질 문제는 다음과 같습니다.


```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```

<!-- nbend -->

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.