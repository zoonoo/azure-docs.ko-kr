---
title: 뉴욕시 안전 데이터
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 뉴욕시 보안 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d8efbd7469ed92b4c323ed3d94315ec8f0f4dc5c
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038986"
---
# <a name="new-york-city-safety-data"></a>뉴욕시 안전 데이터

2010년부터 현재까지의 모든 뉴욕시 311 서비스 요청입니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]


## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 Parquet 형식으로 저장됩니다. 이 데이터 세트는 매일 업데이트되며, 2019년 기준 총 약 1,200만 개의 행(500MB)을 포함합니다.

이 데이터 세트는 2010년부터 현재까지 누적된 기록 레코드를 포함합니다. SDK의 매개 변수 설정을 사용하여 특정 시간 범위의 데이터를 가져올 수 있습니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="additional-information"></a>추가 정보

이 데이터 세트는 뉴욕시 정부 기관에서 제공됩니다. 자세한 내용은 [뉴욕사 웹 사이트](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2010-to-Present/erm2-nwe9)를 참조하세요. [이 데이터 세트 약관](https://www1.nyc.gov/home/terms-of-use.page)을 참조하세요.

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| address | 문자열 | 1,536,593 | 655 EAST 230 STREET 78-15 PARSONS BOULEVARD | 제출자가 제공한 사건 주소의 번지입니다. |
| category | 문자열 | 446 | Noise - Residential HEAT/HOT WATER | 사건 또는 조건(불만 유형)의 주제를 식별하는 계층 구조의 첫 번째 수준입니다. 해당 하위 범주(설명자)를 포함하거나 독립 실행형일 수 있습니다. |
| dataSubtype | 문자열 | 1 | 311_All | “311_All” |
| dataType | 문자열 | 1 | 안전 | “Safety” |
| dateTime | timestamp | 17,332,609 | 2013-01-24 00:00:00 2015-01-08 00:00:00 | 날짜 서비스 요청이 생성되었습니다. |
| latitude | double | 1,513,691 | 40.89187241649303 40.72195913199264 | 사건 위치의 지역 기반 위도입니다. |
| longitude | double | 1,513,713 | -73.86016845296459 -73.80969682426189 | 사건 위치의 지역 기반 경도입니다. |
| 상태 | 문자열 | 13 | 마감 보류 중 | 제출된 서비스 요청의 상태입니다. |
| subcategory | 문자열 | 1,716 | Loud Music/Party ENTIRE BUILDING | 범주(불만 유형)와 연결되며 사건 또는 조건에 대한 추가 정보를 제공합니다. 해당 값은 불만 유형에 따라 달라지며 항상 서비스 요청에 필요하지는 않습니다. |

## <a name="preview"></a>미리 보기

| dataType | dataSubtype | dateTime | category | subcategory | 상태 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 안전 | 311_All | 4/25/2021 2:05:05 AM | Noise - Street/Sidewalk | Loud Music/Party | 진행 중 | 2766 BATH AVENUE | 40.5906129741766 | -73.9847949011337 | null |  |
| 안전 | 311_All | 4/25/2021 2:04:33 AM | Noise - Commercial | Loud Music/Party | 진행 중 | 1033 WEBSTER AVENUE | 40.8285784533256 | -73.9117746958432 | null |  |
| 안전 | 311_All | 4/25/2021 2:04:27 AM | Noise - Residential | Loud Music/Party | 진행 중 | 620 WEST 141 STREET | 40.8241726554395 | -73.9530069547366 | null |  |
| 안전 | 311_All | 4/25/2021 2:04:04 AM | Noise - Residential | Loud Music/Party | 진행 중 | 1647 64 STREET | 40.6218907202382 | -73.9931125332078 | null |  |
| 안전 | 311_All | 4/25/2021 2:04:01 AM | Noise - Residential | Loud Music/Party | 진행 중 | 30 LENOX AVENUE | 40.7991622274945 | -73.9517496365803 | null |  |
| 안전 | 311_All | 4/25/2021 2:03:40 AM | Illegal Parking | Double Parked Blocking Traffic | 진행 중 | 304 WEST 148 STREET | 40.8248229687124 | -73.940696262361 | null |  |
| 안전 | 311_All | 4/25/2021 2:03:31 AM | Noise - Street/Sidewalk | Loud Music/Party | 진행 중 | ADEE AVENUE | 40.8708386263454 | -73.8382363208686 | null |  |
| 안전 | 311_All | 4/25/2021 2:03:18 AM | Noise - Residential | Loud Music/Party | 진행 중 | 340 EVERGREEN AVENUE | 40.6947512704197 | -73.9248330229197 | null |  |
| 안전 | 311_All | 4/25/2021 2:03:13 AM | Noise - Residential | Banging/Pounding | 진행 중 | 25 REMSEN STREET | 40.6948938116483 | -73.9973494607802 | null |  |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** .

```
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_sanfrancisco)** .

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
folder_name = "Safety/Release/city=SanFrancisco"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_sanfrancisco)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_sanfrancisco)** .

```python
# This is a package in preview.
from azureml.opendatasets import SanFranciscoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = SanFranciscoSafety(start_date=start_date, end_date=end_date)
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_sanfrancisco)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=SanFrancisco"
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