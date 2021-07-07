---
title: 미국 국가 고용 시간 및 소득
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 미국 국가 고용 시간 및 소득 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: ffbd29a3aa19b999c9202a0cea36cafee6a4c5f4
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039022"
---
# <a name="us-national-employment-hours-and-earnings"></a>미국 국가 고용 시간 및 소득

CES(Current Employment Statistics) 프로그램은 미국의 급여 대장에 있는 노동자의 농장 이외 고용, 시간 및 소득의 자세한 산업 예상치를 생성합니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

이 데이터 세트에 대한 자세한 정보에 대한 파일이 포함된 [추가 정보](https://download.bls.gov/pub/time.series/ce/ce.txt)는 [원본 데이터 세트 위치](https://download.bls.gov/pub/time.series/ce/)에서 사용할 수 있습니다.

이 데이터 세트는 [BLS(미국 노동 통계국)](https://www.bls.gov/)에서 게시한 [현재 고용 통계 - CES(미국) 데이터](https://www.bls.gov/ces/)에서 제공됩니다. 이 데이터 세트 사용과 관련된 사용 약관은 [연결 및 저작권 정보](https://www.bls.gov/bls/linksite.htm) 및 [중요한 웹 사이트 고지 사항](https://www.bls.gov/bls/website-policies.htm)을 검토하세요.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [미국 주 고용 시간 및 소득](dataset-us-state-employment-earnings.md)
- [미국 지역 실업 통계](dataset-us-local-unemployment.md)
- [미국 노동력 통계](dataset-us-labor-force.md)

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| data_type_code | 문자열 | 37 | 1 10 | https://download.bls.gov/pub/time.series/ce/ce.datatype을 참조하십시오. |
| data_type_text | 문자열 | 37 | ALL EMPLOYEES, THOUSANDS WOMEN EMPLOYEES, THOUSANDS | https://download.bls.gov/pub/time.series/ce/ce.datatype을 참조하십시오. |
| footnote_codes | 문자열 | 2 | nan P |  |
| industry_code | 문자열 | 902 | 30000000 32000000 | 여러 가지 산업이 포함됩니다. https://download.bls.gov/pub/time.series/ce/ce.industry를 참조하세요. |
| industry_name | 문자열 | 895 | Nondurable goods Durable goods | 여러 가지 산업이 포함됩니다. https://download.bls.gov/pub/time.series/ce/ce.industry를 참조하세요. |
| period | 문자열 | 13 | M03 M06 | https://download.bls.gov/pub/time.series/ce/ce.period을 참조하십시오. |
| 시즌별 | 문자열 | 2 | 미국 |  |
| series_id | 문자열 | 26,021 | CEU3100000008 CEU9091912001 | 데이터 세트에서 여러 가지 유형의 데이터 계열을 사용할 수 있습니다. https://download.bls.gov/pub/time.series/ce/ce.series를 참조하세요. |
| series_title | 문자열 | 25,685 | All employees, thousands, durable goods, not seasonally adjusted All employees, thousands, nondurable goods, not seasonally adjusted | 데이터 세트에서 사용할 수 있는 여러 가지 유형의 데이터 계열 제목입니다. https://download.bls.gov/pub/time.series/ce/ce.series를 참조하세요. |
| supersector_code | 문자열 | 22 | 31 60 | 더 높은 수준의 산업 또는 부문 분류입니다. https://download.bls.gov/pub/time.series/ce/ce.supersector을 참조하십시오. |
| supersector_name | 문자열 | 22 | Durable Goods Professional and business services | 더 높은 수준의 산업 또는 부문 분류입니다. https://download.bls.gov/pub/time.series/ce/ce.supersector을 참조하십시오. |
| value | float | 572,372 | 38.5 38.400001525878906 |  |
| 연도 | int | 81 | 2017 2012 |  |

## <a name="preview"></a>미리 보기

| data_type_code | industry_code | supersector_code | series_id | 연도 | period | value | footnote_codes | 시즌별 | series_title | supersector_name | industry_name | data_type_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M04 | 52 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M05 | 65 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M06 | 74 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M07 | 103 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M08 | 108 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M09 | 152 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M10 | 307 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |
| 26 | 5000000 | 5 | CES0500000026 | 1939 | M11 | 248 | nan | S | All employees, 3-month average change, seasonally adjusted, thousands, total private, seasonally adjusted | Total private | Total private | ALL EMPLOYEES, 3-MONTH AVERAGE CHANGE, SEASONALLY ADJUSTED, THOUSANDS |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_pandas_dataframe()
```

```python
usLaborEHENational_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-employment-hours-earnings-national)** .

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
folder_name = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-employment-hours-earnings-national)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborEHENational

usLaborEHENational = UsLaborEHENational()
usLaborEHENational_df = usLaborEHENational.to_spark_dataframe()
```

```python
display(usLaborEHENational_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-employment-hours-earnings-national)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-employment-hours-earnings-national)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ehe_national/"
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