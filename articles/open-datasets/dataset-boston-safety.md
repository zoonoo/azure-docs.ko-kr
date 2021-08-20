---
title: 보스턴 안전 데이터
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 보스턴 안전 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1c9a4917f1b5d2b719a247d111b62897f8063bc9
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039127"
---
# <a name="boston-safety-data"></a>보스턴 안전 데이터

보스턴시에 보고된 311 통화입니다.

[BOS:311](https://www.cityofboston.gov/311/)에 관해 자세히 알아보려면 이 링크를 참조하세요.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 Parquet 형식으로 저장됩니다. 이 데이터 세트는 매일 업데이트되며 2019년 기준 총 약 10만 개의 행(10MB)을 포함합니다.

이 데이터 세트는 2011년부터 현재까지 누적된 기록 레코드를 포함합니다. SDK의 매개 변수 설정을 사용하여 특정 시간 범위의 데이터를 가져올 수 있습니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="additional-information"></a>추가 정보

이 데이터 세트는 보스턴시 정부에서 제공한 것입니다. 자세한 내용은 [보스턴 데이터 세트 사이트](https://data.boston.gov/dataset/311-service-requests)를 참조하세요. 데이터 세트 라이선스는 [ODC PDDL(Open Data Commons Public Domain Dedication and License)](http://opendefinition.org/licenses/odc-pddl/)을 참조하세요.

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| address | 문자열 | 140,612 | \" \" 1 City Hall Plz Boston MA 02108 | 위치입니다. |
| category | 문자열 | 54 | Street Cleaning Sanitation | 서비스 요청의 이유입니다. |
| dataSubtype | 문자열 | 1 | 311_All | “311_All” |
| dataType | 문자열 | 1 | 안전 | “Safety” |
| dateTime | timestamp | 1,529,075 | 2015-07-23 10:51:00 2015-07-23 10:47:00 | 서비스 요청의 시작 날짜 및 시간입니다. |
| latitude | double | 1,622 | 42.3594 42.3603 | 위도 값입니다. 위선은 적도와 평행입니다. |
| longitude | double | 1,806 | -71.0587 -71.0583 | 경도 값입니다. 경선은 위선과 수직을 이루며 모두 양극을 통과합니다. |
| source | 문자열 | 7 | Constituent Call Citizens Connect App | 사례의 원본입니다. |
| 상태 | 문자열 | 2 | Closed Open | 사례 상태입니다. |
| subcategory | 문자열 | 209 | Parking Enforcement Requests for Street Cleaning | 서비스 요청의 유형입니다. |

## <a name="preview"></a>미리 보기

| ataType | dataSubtype | dateTime | category | subcategory | 상태 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 안전 | 311_All | 4/27/2021 11:45:49 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 열기 | 51 Gardner St Allston MA 02134 | 42.3535 | -71.1285 | Citizens Connect App |  |
| 안전 | 311_All | 4/27/2021 11:43:43 PM | Sanitation | Missed Trash/Recycling/Yard Waste/Bulk Item | 열기 | 4 Putnam Pl Roxbury MA 02119 | 42.3298 | -71.0883 | Self Service |  |
| 안전 | 311_All | 4/27/2021 11:37:19 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 열기 | 36 Raven St Dorchester MA 02125 | 42.3177 | -71.0546 | Citizens Connect App |  |
| 안전 | 311_All | 4/27/2021 11:30:00 PM | Sanitation | Missed Trash/Recycling/Yard Waste/Bulk Item | 열기 | 58 Bicknell St Dorchester MA 02121 | 42.2984 | -71.0834 | Constituent Call |  |
| 안전 | 311_All | 4/27/2021 11:10:20 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 열기 | 2000 Commonwealth Ave Brighton MA 02135 | 42.3394 | -71.1585 | Citizens Connect App |  |
| 안전 | 311_All | 4/27/2021 11:06:00 PM | Noise Disturbance | Loud Parties/Music/People | 열기 | INTERSECTION of Lewis St & North St Boston MA | 42.3594 | -71.0587 | Constituent Call |  |
| 안전 | 311_All | 4/27/2021 11:05:00 PM | Enforcement & Abandoned Vehicles | Parking Enforcement | 열기 | 1 Nassau St Boston MA 02111 | 42.3486 | -71.0629 | Constituent Call |  |
| 안전 | 311_All | 4/27/2021 11:00:55 PM | Code Enforcement | Poor Conditions of Property | 열기 | 17 Mercer St South Boston MA 02127 | 42.3332 | -71.0492 | Citizens Connect App |  |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_boston)** .

```
# This is a package in preview.
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_boston)** .

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
folder_name = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_boston)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_boston)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_boston)** .

```python
from azureml.opendatasets import BostonSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = BostonSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
display(safety)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_boston)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Boston"
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