---
title: 미국 우편 번호별 미국 인구
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 미국 우편 번호별 미국 인구를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 2dcf696b996edabbd34af163fd293fdefae8601e
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039019"
---
# <a name="us-population-by-zip-code"></a>미국 우편 번호별 미국 인구

2000년 및 2010년의 10년 단위 인구 조사에서 제공된 각 미국 우편 번호에 대한 성별 및 인종별 미국 인구입니다.

이 데이터 세트는 미국 인구 조사국의 [10년 단위 인구 조사 데이터 세트 API](https://www.census.gov/data/developers/data-sets/decennial-census.html)에서 제공됩니다. 이 데이터 세트 사용과 관련된 사용 약관은 [서비스 약관](https://www.census.gov/data/developers/about/terms-of-service.html) 및 [정책 및 고지 사항](https://www.census.gov/about/policies.html)을 검토하세요.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 Parquet 형식으로 저장되며 2010년에 대한 데이터를 포함합니다.

## <a name="storage-location"></a>스토리지 위치
이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [자치주별 미국 인구](dataset-us-population-county.md)

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| decennialTime | 문자열 | 1 | 2010 | 10년 단위 인구 조사가 수행된 시간(예: 2010년, 2000년)입니다. |
| maxAge | int | 23 | 54 21 | 연령 범위의 최댓값입니다. null일 경우 모든 연령을 포함하거나 연령 범위에 상한값이 없습니다(예: 85세를 초과하는 연령). |
| minAge | int | 23 | 45 30 | 연령 범위의 최솟값입니다. null일 경우 모든 연령을 포함합니다. |
| 채우기(population) | int | 29,274 | 1 2 | 이 부문의 인구입니다. |
| race | 문자열 | 8 | SOME OTHER RACE ALONE BLACK OR AFRICAN AMERICAN ALONE | 인구 조사 데이터의 인종 범주입니다. null일 경우 모든 인종을 포함합니다. |
| sex | 문자열 | 3 | Female Male | 남성 또는 여성입니다. null일 경우 두 성별을 모두 포함합니다. |
| 연도 | int | 1 | 2010 | 10년 단위 시간의 연도(정수)입니다. |
| zipCode | 문자열 | 33,120 | 39218 87420 | 5자리 ZIP Code Tabulation Area(ZCTA5)입니다. |

## <a name="preview"></a>미리 보기

| decennialTime | zipCode | 채우기(population) | race | sex | minAge | maxAge | 연도 |
|-|-|-|-|-|-|-|-|
| 2010 | 77477 | 265 | WHITE ALONE | Female | 15 | 17 | 2010 |
| 2010 | 77477 | 107 | SOME OTHER RACE ALONE | Female | 15 | 17 | 2010 |
| 2010 | 77477 | 12 | SOME OTHER RACE ALONE | Female | 65 | 66 | 2010 |
| 2010 | 77477 | 101 | ASIAN ALONE | Female | 60 | 61 | 2010 |
| 2010 | 77477 | 221 | ASIAN ALONE | Male | 10 | 14 | 2010 |
| 2010 | 77478 | 256 | WHITE ALONE | Female | 15 | 17 | 2010 |
| 2010 | 77478 | 17 | SOME OTHER RACE ALONE | Female | 15 | 17 | 2010 |
| 2010 | 77478 | 3 | SOME OTHER RACE ALONE | Female | 65 | 66 | 2010 |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-decennial-census-zip -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-decennial-census-zip)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationZip

population = UsPopulationZip()
population_df = population.to_pandas_dataframe()
```

```python
population_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-decennial-census-zip -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-decennial-census-zip)** .

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
container_name = "censusdatacontainer"
folder_name = "release/us_population_zip/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-decennial-census-zip -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-decennial-census-zip)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationZip

population = UsPopulationZip()
population_df = population.to_spark_dataframe()
```

```python
display(population_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-decennial-census-zip -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-decennial-census-zip)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "censusdatacontainer"
blob_relative_path = "release/us_population_zip/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=us-decennial-census-zip -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=us-decennial-census-zip)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsPopulationZip

population = UsPopulationZip()
population_df = population.to_spark_dataframe()
```

```python
# Display top 5 rows
display(population_df.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-decennial-census-zip -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-decennial-census-zip)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "censusdatacontainer"
blob_relative_path = "release/us_population_zip/"
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