---
title: 미국 주 고용 시간 및 소득
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 미국 주 고용 시간 및 소득 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a1cb79e704eeae13e8794cad7409e0b945889d22
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039157"
---
# <a name="us-state-employment-hours-and-earnings"></a>미국 주 고용 시간 및 소득

CES(Current Employment Statistics) 프로그램은 미국의 급여 대장에 있는 노동자의 농장 이외 고용, 시간 및 소득의 자세한 산업 예상치를 생성합니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

이 데이터 세트는 [BLS(미국 노동 통계국)](https://www.bls.gov/)에서 게시한 [주 및 대도시 지역 고용](https://www.bls.gov/sae/), [시간 및 소득 데이터](https://www.bls.gov/sae/)에서 제공됩니다. 이 데이터 세트 사용과 관련된 사용 약관은 [연결 및 저작권 정보](https://www.bls.gov/bls/linksite.htm) 및 [중요한 웹 사이트 고지 사항](https://www.bls.gov/bls/website-policies.htm)을 검토하세요.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [미국 국가 고용 시간 및 소득](dataset-us-national-employment-earnings.md)
- [미국 지역 실업 통계](dataset-us-local-unemployment.md)
- [미국 노동력 통계](dataset-us-labor-force.md)

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) |
|-|-|-|-|
| area_code | 문자열 | 446 | 0 31084 |
| area_name | 문자열 | 446 | Statewide Los Angeles-Long Beach-Glendale, CA Metropolitan Division |
| data_type_code | 문자열 | 9 | 1 3 |
| data_type_text | 문자열 | 9 | All Employees, In Thousands Average Weekly Hours of All Employees |
| footnote_codes | 문자열 | 3 | nan P |
| industry_code | 문자열 | 343 | 0 5000000 |
| industry_name | 문자열 | 343 | Total Nonfarm Total Private |
| period | 문자열 | 13 | M04 M05 |
| 시즌별 | 문자열 | 2 | 미국 |
| series_id | 문자열 | 23,853 | SMU12000000000000001 SMU36000000000000001 |
| state_code | 문자열 | 53 | 6 48 |
| state_name | 문자열 | 53 | California Texas |
| supersector_code | 문자열 | 22 | 90 60 |
| supersector_name | 문자열 | 22 | Government Professional and Business Services |
| value | float | 132,565 | 0.30000001192092896 0.10000000149011612 |
| 연도 | int | 81 | 2014 2018 |

## <a name="preview"></a>미리 보기

| area_code | state_code | data_type_code | industry_code | supersector_code | series_id | 연도 | period | value | footnote_codes | 시즌별 | supersector_name | industry_name | data_type_text | state_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M04 | 0.2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M05 | 0.2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M06 | 0.1 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M07 | 0.1 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M08 | 0.2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M09 | 0.2 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregon | Bend-Redmond, OR |
| 13460 | 41 | 26 | 0 | 0 | SMS41134600000000026 | 1990 | M10 | 0.1 | nan | S | Total Nonfarm | Total Nonfarm | All Employees, 3-month average change, In Thousands, seasonally adjusted | Oregon | Bend-Redmond, OR |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_pandas_dataframe()
```

```python
usLaborEHEState_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-state)** .

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
container_name = "laborstatisticscontainer"
folder_name = "ehe_state/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-state)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHEState

usLaborEHEState = UsLaborEHEState()
usLaborEHEState_df = usLaborEHEState.to_spark_dataframe()
```

```python
display(usLaborEHEState_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-state)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-state)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_state/"
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

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.