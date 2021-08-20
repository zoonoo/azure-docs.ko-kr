---
title: 샌프란시스코 안전 데이터
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 샌프란시스코 보안 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 4502d51054f43b7b1d876443c12f67418ba84060
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982459"
---
# <a name="san-francisco-safety-data"></a>샌프란시스코 안전 데이터

샌프란시스코 소방서에서 서비스 및 311 사례를 요청합니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Fire Calls-For-Service에는 통화에 대한 모든 소방대원 응답이 포함됩니다. 각 레코드에는 전화번호, 사건 번호, 주소, 대원 식별자, 통화 유형 및 처리 상태가 포함됩니다. 모든 관련 시간 간격도 포함됩니다. 이 데이터 세트는 응답을 기반으로 하고 대부분의 통화가 여러 대원과 관련되므로 각 전화번호에 대한 레코드가 여러 개 있습니다. 주소는 특정 주소가 아닌 블록 번호, 교차로 또는 공중전화 박스와 관련이 있습니다.

311 사례에는 장소 또는 사물(예: 공원, 거리 또는 건물)과 관련되고 2008년 7월 1일 이후에 생성된 사례가 포함됩니다. 사용자가 자신의 요구 사항에 대해 기록한 사례는 제외됩니다. 예를 들어, 속성 또는 사업세 문의, 주차 허가 요청 등이 이에 해당합니다. 자세한 내용은 [프로그램 링크](https://support.datasf.org/help/311-case-data-faq)를 참조하세요.
 
## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 Parquet 형식으로 저장됩니다. 이 데이터 세트는 매일 업데이트되며 2019년 기준 약 600만 개의 행(400MB)을 포함합니다.

이 데이터 세트는 2015년부터 현재까지 누적된 기록 레코드를 포함합니다. SDK의 매개 변수 설정을 사용하여 특정 시간 범위의 데이터를 가져올 수 있습니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [소방서 신고 전화](https://data.sfgov.org/Public-Safety/Fire-Department-Calls-for-Service/nuek-vuh3)
- [311 사례](https://data.sfgov.org/City-Infrastructure/311-Cases/vw6y-z8j6)

## <a name="additional-information"></a>추가 정보

이 데이터 세트는 샌프란시스코시 정부에서 제공됩니다. [이 데이터 세트 사용 약관](https://datasf.org/opendata/terms-of-use/)을 참조하세요.

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| address | 문자열 | 280,652 | 6TH ST의 특정 주소 0 블록과 관련 없음 | 사건의 주소(참고: 발신자 프라이버시를 보호하기 위해 주소 및 위치는 거리의 중간 블록, 교차로 또는 가장 가까운 공중전화 박스 위치로 일반화됨)입니다. |
| category | 문자열 | 108 | 도로 및 인도에서 생명에 위협이 될 수 있는 항목 제거 | 911 화재 신고 전화의 통화 유형 그룹 또는 311 서비스 요청 유형의 사람이 읽을 수 있는 이름입니다. |
| dataSubtype | 문자열 | 2 | 911_Fire 311_All | ‘911_Fire’ 또는 ‘311_All’. |
| dataType | 문자열 | 1 | 안전 | “Safety” |
| dateTime | timestamp | 6,496,563 | 2020-10-19 12:28:08 2020-07-28 06:40:26 | 서비스 요청이 수행되고 화재 신고 전화를 받은 날짜 및 시간입니다. |
| latitude | double | 1,615,369 | 37.777624238929 37.786117211838 | 위치의 위도로, WGS84 프로젝션을 사용합니다. |
| longitude | double | 1,554,612 | -122.39998111124 -122.419854245692 | 위치의 경도로, WGS84 프로젝션을 사용합니다. |
| source | 문자열 | 9 | 휴대폰/Open311 | 서비스 요청을 받은 메커니즘 또는 경로(일반적으로 ‘전화’, ‘문자/SMS’, ‘웹 사이트’, ‘모바일 앱’, ‘Twitter’ 등)이지만 용어는 시스템에 따라 달라질 수 있습니다. |
| 상태 | 문자열 | 3 | Closed Open | 서비스 요청의 현재 상태에 대한 단일 단어 표시입니다. (참고: GeoReport V2는 ‘미결’과 ‘종결’만 허용함) |
| subcategory | 문자열 | 1,270 | 의료 사고 대규모 항목 | 911 화재 신고 전화의 통화 유형 또는 311 사례에 대한 서비스 요청 하위 유형의 사람이 읽을 수 있는 이름입니다. |

## <a name="preview"></a>미리 보기

| dataType | dataSubtype | dateTime | category | subcategory | 상태 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 안전 | 911_Fire | 2021/4/26 오전 2:56:13 | 생명에 위협되지 않음 | 의료 사고 | null | GEARY ST 700 블록 | 37.7863607914647 | -122.415616900246 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:56:13 | 생명에 위협되지 않음 | 의료 사고 | null | GEARY ST 700 블록 | 37.7863607914647 | -122.415616900246 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:54:03 | 생명에 위협되지 않음 | 의료 사고 | null | ESSEX ST 0 블록 | 37.7860048266229 | -122.395077258809 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:54:03 | 생명에 위협되지 않음 | 의료 사고 | null | ESSEX ST 0 블록 | 37.7860048266229 | -122.395077258809 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:52:17 | 생명에 위협되지 않음 | 의료 사고 | null | 29TH AVE 700 블록 | 37.7751770865322 | -122.488604397217 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:50:28 | 생명에 위협이 될 수 있음 | 의료 사고 | null | GEARY ST 1000 블록 | 37.7857350982044 | -122.420555240691 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:50:28 | 생명에 위협이 될 수 있음 | 의료 사고 | null | GEARY ST 1000 블록 | 37.7857350982044 | -122.420555240691 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:33:52 | 생명에 위협되지 않음 | 의료 사고 | null | BELVEDERE ST 100 블록 | 37.767791696654 | -122.449332294394 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:33:52 | 생명에 위협되지 않음 | 의료 사고 | null | BELVEDERE ST 100 블록 | 37.767791696654 | -122.449332294394 | null |  |
| 안전 | 911_Fire | 2021/4/26 오전 2:33:51 | 생명에 위협이 될 수 있음 | 의료 사고 | null | 6TH ST 100 블록 | 37.7807920802756 | -122.408385745499 | null |  |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_newyork)** .

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas
```

```python
# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "citydatacontainer"
folder_name = "Safety/Release/city=NewYorkCity"
```

```python
from azure.storage.blob import BlockBlobServicefrom azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

if azure_storage_account_name is None or azure_storage_sas_token is None:
    raise Exception(
        "Provide your specific name and key for your Azure Storage account--see the Prerequisites section earlier.")

print('Looking for the first parquet under the folder ' +
      folder_name + ' in container "' + container_name + '"...')
container_url = f"https://{azure_storage_account_name}.blob.core.windows.net/"
blob_service_client = BlobServiceClient(
    container_url, azure_storage_sas_token if azure_storage_sas_token else None)

container_client = blob_service_client.get_container_client(container_name)
blobs = container_client.list_blobs(folder_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
targetBlobName = ''
for blob in sorted_blobs:
    if blob.name.startswith(folder_name) and blob.name.endswith('.parquet'):
        targetBlobName = blob.name
        break

print('Target blob to download: ' + targetBlobName)
_, filename = os.path.split(targetBlobName)
blob_client = container_client.get_blob_client(targetBlobName)
with open(filename, 'wb') as local_file:
    blob_client.download_blob().download_to_stream(local_file)
```

```python
# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)
```

```python
# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_newyork)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_newyork)** .

```python
# This is a package in preview.
from azureml.opendatasets import NycSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = NycSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_newyork)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=NewYorkCity"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="examples"></a>예

- GitHub에서 [City Safety Analytics](https://github.com/scottcounts/CitySafety) 예제를 참조하세요.


## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.
