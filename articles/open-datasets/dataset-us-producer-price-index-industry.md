---
title: 미국 생산자 물가 지수 산업
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 미국 생산자 물가 지수 산업 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: cd0662fc4990e6a2baf0a29ba2e96cec50509a05
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039016"
---
# <a name="us-consumer-price-index-industry"></a>미국 소비자 물가 지수 산업

PPI(생산자 물가 지수)는 국내 생산자가 얻는 산출물 판매 가격의 시간에 따른 평균 변동을 측정한 값입니다. PPI에 포함된 가격은 해당 제품 및 서비스의 첫 번째 상거래에서 나온 것입니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

Producer Price Index Revision-Current Series(생산자 물가 지수 개정-현재 계열) 지수는 NAICS(북미 산업 분류 시스템)에 따라 구성되는 생산자의 순 산출물에 대한 가격 동향을 반영합니다. PC 데이터 세트는 생산성, 생산, 고용, 임금 및 수익을 포함한 광범위한 NAICS 기반 경제 시계열과 호환됩니다.

PPI 체계는 미국 경제의 제품 생산 부문에 속한 모든 산업(광업, 제조업, 농업, 어업, 임업, 천연가스업, 전기업, 건설업뿐 아니라 생산 부문에서 제조된 제품의 경쟁 제품(예: 폐기물) 재활용업)의 산출물로 구성됩니다. 또한 2011년 1월 기준 PPI 프로그램은 서비스 부문 산출물의 3/4 이상을 포함하며 도소매업, 운송 및 창고업, 정보업, 금융 및 보험업, 부동산 중개/임대/리스업, 전문/과학/기술 서비스업, 행정/지원/폐기물 관리 서비스업, 건강 관리 및 사회 서비스업, 숙박업 등의 산업 부문에서 선택된 산업의 데이터를 게시합니다.

이 데이터 세트에 대한 자세한 정보를 포함하는 [추가 정보](https://download.bls.gov/pub/time.series/wp/wp.txt)는 [원본 데이터 세트 위치](https://download.bls.gov/pub/time.series/wp/)에서 사용할 수 있습니다. 추가 정보는 [FAQ](https://www.bls.gov/ppi/ppifaq.htm)에서 확인할 수 있습니다.

이 데이터 세트는 [미국 BLS(노동 통계국)](https://www.bls.gov/)에서 게시한 [생산자 물가 지수 데이터](https://www.bls.gov/ppi/)에서 생성됩니다. 이 데이터 세트 사용과 관련된 사용 약관은 [연결 및 저작권 정보](https://www.bls.gov/bls/linksite.htm) 및 [중요한 웹 사이트 고지 사항](https://www.bls.gov/bls/website-policies.htm)을 검토하세요.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [미국 소비자 물가 지수](dataset-us-consumer-price-index.md)
- [미국 생산자 물가 지수 - 상품](dataset-us-producer-price-index-commodities.md)

## <a name="columns"></a>열

| 이름 | 데이터 형식 | 고유한 | 값(샘플) | Description |
|-|-|-|-|-|
| footnote_codes | 문자열 | 3 | nan P | 데이터 계열의 각주를 식별합니다. 대부분 값은 null입니다. https://download.bls.gov/pub/time.series/pc/pc.footnote을 참조하세요. |
| industry_code | 문자열 | 1,064 | 221122 325412 | 산업의 NAICS 코드입니다. 코드 및 이름은 https://download.bls.gov/pub/time.series/pc/pc.industry 를 참조하세요. |
| industry_name | 문자열 | 842 | Electric power distribution Pharmaceutical preparation manufacturing | 산업 코드에 해당하는 이름입니다. 코드 및 이름은 https://download.bls.gov/pub/time.series/pc/pc.industry 를 참조하세요. |
| period | 문자열 | 13 | M06 M07 | 데이터가 관찰된 기간을 식별합니다. 전체 목록은 https://download.bls.gov/pub/time.series/pc/pc.period 를 참조하세요. |
| product_code | 문자열 | 4,822 | 324121P 316--- | 데이터 관찰이 참조하는 제품을 식별하는 코드입니다. 산업 코드, 제품 코드 및 제품 이름의 매핑은 https://download.bls.gov/pub/time.series/pc/pc.product 를 참조하세요. |
| product_name | 문자열 | 3,313 | Primary products Secondary products | 데이터 관찰이 참조하는 제품의 이름입니다. 산업 코드, 제품 코드 및 제품 이름의 매핑은 https://download.bls.gov/pub/time.series/pc/pc.product 를 참조하세요. |
| seasonal | 문자열 | 1 | U | 데이터가 계절에 따라 조정되었는지 여부를 식별하는 코드입니다. S=Seasonally Adjusted; U=Unadjusted |
| series_id | 문자열 | 4,822 | PCU332323332323M PCU333415333415C | 특정 계열을 식별하는 코드입니다. 시계열은 일관된 시간 간격으로 긴 기간 동안 관찰된 데이터 세트를 의미합니다. 코드, 이름, 시작 및 종료 연도 등의 시리즈에 관한 자세한 내용은 https://download.bls.gov/pub/time.series/pc/pc.series 를 참조하세요. |
| series_title | 문자열 | 4,588 | PPI industry data for Electric power distribution-West South Central, not seasonally adjusted PPI industry data for Electric power distribution-Pacific, not seasonally adjusted |  |
| value | float | 7,658 | 100.0 100.4000015258789 | 항목에 대한 가격 지수입니다. |
| 연도 | int | 22 | 2015 2017 | 관찰 연도를 식별합니다. |

## <a name="preview"></a>미리 보기

| product_code | industry_code | series_id | 연도 | period | value | footnote_codes | seasonal | series_title | industry_name | product_name |
|-|-|-|-|-|-|-|-|-|-|-|
| 2123240 | 212324 | PCU2123242123240 | 1998 | M01 | 117 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M02 | 116.9 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M03 | 116.3 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M04 | 116 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M05 | 116.2 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M06 | 116.3 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |
| 2123240 | 212324 | PCU2123242123240 | 1998 | M07 | 116.6 | nan | U | PPI industry data for Kaolin and ball clay mining-Kaolin and ball clay, not seasonally adjusted | Kaolin and ball clay mining | Kaolin and ball clay |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_pandas_dataframe()
```

```python
labor_df.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=us-producer-price-index-industry)** .

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
folder_name = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=us-producer-price-index-industry)** .

```python
# This is a package in preview.
from azureml.opendatasets import UsLaborPPIIndustry

labor = UsLaborPPIIndustry()
labor_df = labor.to_spark_dataframe()
```

```python
display(labor_df.limit(5))
```

<!-- nbend -->
 
# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=us-producer-price-index-industry)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=us-producer-price-index-industry)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "laborstatisticscontainer"
blob_relative_path = "ppi_industry/"
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