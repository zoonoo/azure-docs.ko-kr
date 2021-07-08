---
title: 미국 소비자 물가 지수
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 미국 소비자 물가 지수 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: fffe6b1521f3f9ab4973a64f03ef52e4a9019658
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039031"
---
# <a name="us-consumer-price-index"></a>미국 소비자 물가 지수

CPI(소비자 물가 지수)는 도시 소비자가 장바구니 소비재 및 서비스에 지불한 가격의 시간에 따른 평균 변동을 측정한 값입니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

이 데이터 세트에 대한 자세한 정보를 포함하는 [추가 정보](https://download.bls.gov/pub/time.series/cu/cu.txt)는 [원본 데이터 세트 위치](https://download.bls.gov/pub/time.series/cu/)에서 사용할 수 있습니다.

이 데이터 세트는 [미국 BLS(노동 통계국)](https://www.bls.gov/)에서 게시한 [소비자 물가 지수 데이터](https://www.bls.gov/cpi/)에서 생성됩니다. 사용 약관에 대해서는 [링크 및 저작권 정보](https://www.bls.gov/bls/linksite.htm)와 [중요한 웹 사이트 고지 사항](https://www.bls.gov/bls/website-policies.htm)을 검토하세요.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도의 경우 미국 동부에서 컴퓨팅 리소스를 찾는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [미국 생산자 물가 지수 - 산업](dataset-us-producer-price-index-industry.md)
- [미국 생산자 물가 지수 - 상품](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| area_code | 문자열 | 70 | 0000 0300 | 특정 지역을 식별하는 데 사용되는 고유한 코드입니다. 전체 지역 코드는 http://download.bls.gov/pub/time.series/cu/cu.area 를 참조하세요. |
| area_name | 문자열 | 69 | U.S. city average South | 특정 지역의 이름입니다. 모든 지역 이름 및 코드는 https://download.bls.gov/pub/time.series/cu/cu.area 를 참조하세요. |
| footnote_codes | 문자열 | 3 | nan U | 데이터 계열의 각주를 식별합니다. 대부분 값은 null입니다. |
| item_code | 문자열 | 515 | SA0E SAF11 | 데이터 관찰이 적용되는 항목을 식별합니다. 모든 항목 이름 및 코드는 https://download.bls.gov/pub/time.series/cu/cu.item 을 참조하세요. |
| item_name | 문자열 | 515 | Energy Food at home | 항목의 전체 이름입니다. 항목 이름 및 코드는 https://download.bls.gov/pub/time.series/cu/cu.txt 를 참조하세요. |
| period | 문자열 | 16 | S01 S02 | 데이터가 관찰된 기간을 식별합니다. 형식: M01-M13 또는 S01-S03(M=Monthly, M13=Annual Avg, S=Semi-Annually). 예: M06=6월. 기간 이름 및 코드는 https://download.bls.gov/pub/time.series/cu/cu.period 를 참조하세요. |
| periodicity_code | 문자열 | 3 | R S | 데이터 관찰 빈도입니다. S=Semi-Annual; R=Regular. |
| 시즌별 | 문자열 | 1,043 | 미국 | 데이터가 계절에 따라 조정되었는지 여부를 식별하는 코드입니다. S=Seasonally Adjusted; U=Unadjusted. |
| series_id | 문자열 | 16,683 | CWURS400SA0E CWUR0100SA0E | 특정 계열을 식별하는 코드입니다. 시계열은 일관된 시간 간격(월별, 분기별, 연 2회, 연간)으로 장기간 관찰된 데이터 세트를 의미합니다. 일반적으로 BLS 시계열 데이터는 월 간격으로 생성되며 가격이 매월 수집되는 특정 지역의 특정 소비자 항목에서 고용률이 매월 기록되는 특정 산업 근로자 범주 등에 이르는 데이터를 나타냅니다. 자세한 내용은 https://download.bls.gov/pub/time.series/cu/cu.txt을 참조하세요. |
| series_title | 문자열 | 8,336 | Alcoholic drinks in U.S. city average, all urban consumers, not seasonally adjusted Transportation in Los Angeles-Long Beach-Anaheim, CA, all urban consumers, not seasonally adjusted | 해당 series_id의 계열 이름입니다. 시리즈 ID 및 이름은 https://download.bls.gov/pub/time.series/cu/cu.series 를 참조하세요. |
| value | float | 310,603 | 100.0 101.0999984741211 | 항목에 대한 가격 지수입니다. |
| 연도 | int | 25 | 2018 2017 | 관찰 연도를 식별합니다. |

## <a name="preview"></a>미리 보기

| area_code | item_code | series_id | 연도 | period | value | footnote_codes | 시즌별 | periodicity_code | series_title | item_name | area_name |
|-|-|-|-|-|-|-|-|-|-|-|-|
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | Electricity in San Diego-Carlsbad, CA, all urban consumers, not seasonally adjusted | Electricity | San Diego-Carlsbad, CA |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | Electricity in San Diego-Carlsbad, CA, all urban consumers, not seasonally adjusted | Electricity | San Diego-Carlsbad, CA |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | Electricity in San Diego-Carlsbad, CA, all urban consumers, not seasonally adjusted | Electricity | San Diego-Carlsbad, CA |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | Electricity in San Diego-Carlsbad, CA, all urban consumers, not seasonally adjusted | Electricity | San Diego-Carlsbad, CA |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | Electricity in San Diego-Carlsbad, CA, all urban consumers, not seasonally adjusted | Electricity | San Diego-Carlsbad, CA |
| S49E | SEHF01 | CUURS49ESEHF01 | 2017 | M12 | 279.974 | nan | U | R | Electricity in San Diego-Carlsbad, CA, all urban consumers, not seasonally adjusted | Electricity | San Diego-Carlsbad, CA |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-consumer-price-index)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_pandas_dataframe()
```

```python
usLaborCPI_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-consumer-price-index)** .

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
folder_name = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-consumer-price-index)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborCPI

usLaborCPI = UsLaborCPI()
usLaborCPI_df = usLaborCPI.to_spark_dataframe()
```

```python
display(usLaborCPI_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-consumer-price-index)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-consumer-price-index)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "cpi/"
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