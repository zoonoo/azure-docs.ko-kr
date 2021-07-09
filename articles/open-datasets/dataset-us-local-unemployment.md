---
title: 미국 지역 실업 통계
titleSuffix: Azure Open Datasets
description: Azure Open Datasets의 미국 지역 실업 통계 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 13af967282d1f9be8f289612936ab7d33a9fce11
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039025"
---
# <a name="us-local-area-unemployment-statistics"></a>미국 지역 실업 통계

LAUS(Local Area Unemployment Statistics) 프로그램은 미국의 인구 조사 지역 및 구역, 주, 자치주, 대도시 지역 및 여러 도시에 대한 월별 및 연간 고용, 실업 및 노동력 데이터를 생성합니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

이 데이터 세트에 대한 자세한 정보를 포함하는 [추가 정보](https://download.bls.gov/pub/time.series/la/la.txt)는 [원본 데이터 세트 위치](https://download.bls.gov/pub/time.series/la/)에서 사용할 수 있습니다.

이 데이터 세트는 [BLS(미국 노동 통계국)](https://www.bls.gov/)에서 게시한 [현지 실업 통계 데이터](https://www.bls.gov/lau/)에서 제공됩니다. 이 데이터 세트 사용과 관련된 사용 약관은 [연결 및 저작권 정보](https://www.bls.gov/bls/linksite.htm) 및 [중요한 웹 사이트 고지 사항](https://www.bls.gov/bls/website-policies.htm)을 검토하세요.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [미국 국가 고용 시간 및 소득](dataset-us-national-employment-earnings.md)
- [미국 주 고용 시간 및 소득](dataset-us-state-employment-earnings.md)
- [미국 노동력 통계](dataset-us-labor-force.md)

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| area_code | 문자열 | 8,290 | ST3400000000000 RD8200000000000 | 지리적 지역을 식별하는 코드입니다. https://download.bls.gov/pub/time.series/la/la.area을 참조하세요. |
| area_text | 문자열 | 8,238 | District of Columbia Oregon | 지리적 지역의 이름입니다. https://download.bls.gov/pub/time.series/la/la.area를 참조하세요. |
| area_type_code | 문자열 | 14 | F G | 지역 유형을 정의하는 고유한 코드입니다. https://download.bls.gov/pub/time.series/la/la.area_type를 참조하세요. |
| areatype_text | 문자열 | 14 | Counties and equivalents Cities and towns above 25,000 population | 지역 유형의 이름입니다. |
| footnote_codes | 문자열 | 5 | nan P |  |
| measure_code | 문자열 | 4 | 5 4 | 측정된 요소를 식별하는 코드입니다. 03: 실업률, 04: 실업, 05: 고용, 06: 노동력 https://download.bls.gov/pub/time.series/la/la.measure을 참조하세요. |
| measure_text | 문자열 | 4 | employment unemployment | 측정된 요소의 이름입니다. https://download.bls.gov/pub/time.series/la/la.measure를 참조하세요. |
| period | 문자열 | 13 | M07 M05 | 기간(특히 월)을 식별합니다. https://download.bls.gov/pub/time.series/la/la.period를 참조하세요. |
| 시즌별 | 문자열 | 2 | 미국 |  |
| series_id | 문자열 | 33,476 | LASST160000000000006 LASST200000000000006 | 계열을 식별하는 코드입니다. 시리즈의 전체 목록은 https://download.bls.gov/pub/time.series/la/la.series 를 참조하세요. |
| series_title | 문자열 | 33,268 | Employment: Philadelphia County/city, PA (U) Labor Force: Manassas city, VA (U) | 계열을 식별하는 제목입니다. 시리즈의 전체 목록은 https://download.bls.gov/pub/time.series/la/la.series 를 참조하세요. |
| srd_code | 문자열 | 53 | 48 23 | 주, 지역 또는 지구 코드입니다. |
| srd_text | 문자열 | 53 | Texas Maine |  |
| value | float | 600,099 | 4.0 5.0 | 특정 측정값입니다. |
| 연도 | int | 44 | 2009 2008 |  |

## <a name="preview"></a>미리 보기

| area_code | area_type_code | srd_code | measure_code | series_id | 연도 | period | value | footnote_codes | 시즌별 | series_title | measure_text | srd_text | areatype_text | area_text |
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M01 | 4.7 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) | unemployment rate | 뉴욕 | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M02 | 4.7 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) | unemployment rate | 뉴욕 | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M03 | 4.2 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) | unemployment rate | 뉴욕 | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M04 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) | unemployment rate | 뉴욕 | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M05 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) | unemployment rate | 뉴욕 | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M06 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) | unemployment rate | 뉴욕 | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |
| CA3653200000000 | E | 36 | 3 | LAUCA365320000000003 | 2000 | M07 | 3.6 | nan | U | Unemployment Rate: Syracuse-Auburn, NY Combined Statistical Area (U) | unemployment rate | 뉴욕 | Combined areas | Syracuse-Auburn, NY Combined Statistical Area |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_pandas_dataframe()
```

```python
usLaborLAUS_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-local-area-unemployment-statistics)** .

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
folder_name = "laus/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-local-area-unemployment-statistics)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborLAUS

usLaborLAUS = UsLaborLAUS()
usLaborLAUS_df = usLaborLAUS.to_spark_dataframe()
```

```python
display(usLaborLAUS_df.limit(5))
```

<!-- nbend -->

 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-local-area-unemployment-statistics)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-local-area-unemployment-statistics)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "laus/"
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