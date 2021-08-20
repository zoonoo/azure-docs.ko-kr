---
title: 미국 생산자 물가 지수 - 상품
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 미국 생산자 물가 지수 - 삼품 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 224a5d0278237d5a7d87ee8e3c9adaedb71f193a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039160"
---
# <a name="us-producer-price-index---commodities"></a>미국 생산자 물가 지수 - 상품

PPI(생산자 물가 지수)는 국내 생산자가 얻는 산출물 판매 가격의 시간에 따른 평균 변동을 측정한 값입니다. PPI에 포함된 가격은 해당 제품 및 서비스의 첫 번째 상거래에서 나온 것입니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

매월 개별 제품 및 제품 그룹에 대해 약 10,000개의 PPI가 릴리스됩니다. PPI는 미국 경제의 제품 생산 부문에 속한 거의 모든 산업(광업, 제조업, 농업, 어업, 임업, 천연가스업, 전기업, 건설업뿐 아니라 생산 부문에서 제조된 제품의 경쟁 제품(예: 폐기물) 재활용업)의 산출물로 구성됩니다. 2007 Economic Census에서 보고된 수익 기준으로 측정된 바에 따르면 PPI 프로그램은 약 72%의 서비스 부문 산출물에 적용됩니다. 데이터에는 도매 및 소매업, 운송 및 창고업, 정보, 금융 및 보험, 부동산 중개, 임대 및 리스, 전문, 과학 및 기술 서비스, 행정, 지원 및 폐기물 관리 서비스, 건강 관리 및 사회적 지원, 숙박업 등의 부문에 속한 산업이 포함됩니다.

이 데이터 세트에 대한 자세한 정보를 포함하는 [추가 정보](https://download.bls.gov/pub/time.series/wp/wp.txt)는 [원본 데이터 세트 위치](https://download.bls.gov/pub/time.series/wp/)에서 사용할 수 있습니다. 추가 정보는 [FAQ](https://www.bls.gov/ppi/ppifaq.htm)에서 확인할 수 있습니다.

이 데이터 세트는 [미국 BLS(노동 통계국)](https://www.bls.gov/)에서 게시한 [생산자 물가 지수 데이터](https://www.bls.gov/ppi/)에서 생성됩니다. 이 데이터 세트 사용과 관련된 사용 약관은 [연결 및 저작권 정보](https://www.bls.gov/bls/linksite.htm) 및 [중요한 웹 사이트 고지 사항](https://www.bls.gov/bls/website-policies.htm)을 검토하세요.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [미국 소비자 물가 지수](dataset-us-consumer-price-index.md)
- [미국 생산자 물가 지수 - 산업](dataset-us-producer-price-index-industry.md)

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| footnote_codes | 문자열 | 3 | nan P | 데이터 계열의 각주를 식별합니다. 대부분 값은 null입니다. https://download.bls.gov/pub/time.series/wp/wp.footnote을 참조하세요. |
| group_code | 문자열 | 56 | 02 01 | 지수가 적용되는 주요 상품 그룹을 식별하는 코드입니다. 그룹 코드 및 이름은 https://download.bls.gov/pub/time.series/wp/wp.group 을 참조하세요. |
| group_name | 문자열 | 56 | Processed foods and feeds Farm products | 지수가 적용되는 주요 상품 그룹의 이름입니다. 그룹 코드 및 이름은 https://download.bls.gov/pub/time.series/wp/wp.group 을 참조하세요. |
| item_code | 문자열 | 2,949 | 1 11 | 데이터 관찰이 적용되는 항목을 식별합니다. 항목 코드 및 이름은 https://download.bls.gov/pub/time.series/wp/wp.item 을 참조하세요. |
| item_name | 문자열 | 3,410 | Warehousing, storage, and related services Passenger car rental | 항목의 전체 이름입니다. 항목 코드 및 이름은 https://download.bls.gov/pub/time.series/wp/wp.item 을 참조하세요. |
| period | 문자열 | 13 | M06 M07 | 데이터가 관찰된 기간을 식별합니다. 기간 값의 목록은 https://download.bls.gov/pub/time.series/wp/wp.period 를 참조하세요. |
| 시즌별 | 문자열 | 2 | 미국 | 데이터가 계절에 따라 조정되었는지 여부를 식별하는 코드입니다. S=Seasonally Adjusted; U=Unadjusted |
| series_id | 문자열 | 5,458 | WPU101 WPU091 | 특정 계열을 식별하는 코드입니다. 시계열은 일관된 시간 간격으로 긴 기간 동안 관찰된 데이터 세트를 의미합니다. 코드, 이름, 시작 및 종료 연도 등의 시리즈에 관한 자세한 내용은 https://download.bls.gov/pub/time.series/wp/wp.series 를 참조하세요. |
| series_title | 문자열 | 4,379 | PPI Commodity data for Mining services, not seasonally adjusted PPI Commodity data for Metal treatment services, not seasonally adjusted | 특정 계열의 제목입니다. 시계열은 일관된 시간 간격으로 긴 기간 동안 관찰된 데이터 세트를 의미합니다. ID, 이름, 시작 및 종료 연도 등의 시리즈에 관한 자세한 내용은 https://download.bls.gov/pub/time.series/wp/wp.series 를 참조하세요. |
| value | float | 6,788 | 100.0 99.0999984741211 | 항목에 대한 가격 지수입니다. |
| 연도 | int | 26 | 2018 2017 | 관찰 연도를 식별합니다. |

## <a name="preview"></a>미리 보기

| item_code | group_code | series_id | 연도 | period | value | footnote_codes | 시즌별 | series_title | group_name | item_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 120922 | 05 | WPU05120922 | 2008 | M06 | 100 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M07 | 104.6 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M08 | 104.4 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M09 | 98.3 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M10 | 101.5 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |
| 120922 | 05 | WPU05120922 | 2008 | M11 | 95.2 | nan | U | PPI Commodity data for Fuels and related products and power-Prepared bituminous coal underground mine, mechanically crushed/screened/sized only, not seasonally adjusted | Fuels and related products and power | Prepared bituminous coal underground mine, mechanically crushed/screened/sized only |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->


# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-commodities)** .

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
folder_name = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-commodities)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPICommodity

labor = UsLaborPPICommodity()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-commodities)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-commodities)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_commodity/"
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