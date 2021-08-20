---
title: NYC 택시 및 리무진 노란색 데이터 세트
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 NYC 택시 및 리무진 노란색 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 5bf2a3b363c7d8a1cd0b10b1c958c4cfbb567deb
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039169"
---
# <a name="nyc-taxi--limousine-commission---yellow-taxi-trip-records"></a>NYC 택시 및 리무진 협회 - 노란색 택시 이동 레코드

노란색 택시 이동 레코드에는 승차 및 하차 날짜/시간, 승차 및 하단 위치, 이동 거리, 항목별 요금, 요율 종류, 지불 유형 및 운전자가 보고한 승객 수를 캡처하는 필드가 포함됩니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 Parquet 형식으로 저장됩니다. 2018년 기준 총 약 15억 개의 행(50GB)이 있습니다.

이 데이터 세트는 2009년부터 2018년까지 누적된 기록 레코드를 포함합니다. SDK의 매개 변수 설정을 사용하여 특정 시간 범위의 데이터를 가져올 수 있습니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="additional-information"></a>추가 정보

NYC TLC(택시 및 리무진 위원회):

데이터는 TPEP/LPEP(Taxicab & Livery Passenger Enhancement Programs)에 따라 승인된 기술 공급 기업에 의해 수집되어 NYC TLC(택시 및 리무진 위원회)에 제공되었습니다. 이동 데이터는 TLC에서 만든 것이 아니며, TLC는 이 데이터의 정확성에 관해 어떠한 진술도 하지 않습니다.

[원래 데이터 세트 위치](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) 및 [원래 사용 약관](https://www1.nyc.gov/home/terms-of-use.page)을 확인합니다.

## <a name="columns"></a>열
| 이름                 | 데이터 형식 | 고유한      | 값(샘플)                         | Description                                                                                                                                                                                                                                            |
|----------------------|-----------|-------------|-----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | 문자열    | 265         | 161 236                                 | 택시미터가 해제된 TLC 택시 승차 구역입니다.                                                                                                                                                                                                   |
| endLat               | double    | 961,994     | 41.366138 40.75                         |                                                                                                                                                                                                                                                        |
| endLon               | double    | 1,144,935   | -73.137393 -73.9824                     |                                                                                                                                                                                                                                                        |
| extra                | double    | 877         | 0.5 1.0                                 | 기타 추가 비용 및 추가 요금입니다. 현재 혼잡 시간대 요금 $0.50 및 야간할증 요금 $1만 포함됩니다.                                                                                                                                   |
| fareAmount           | double    | 18,935      | 6.5 4.5                                 | 미터로 계산된 시간/거리 병산 요금입니다.                                                                                                                                                                                                    |
| improvementSurcharge | 문자열    | 60          | 0.3 0                                   | 기본요금에서 $0.30 개선 추가 요금이 평가되었습니다. 개선 추가 요금은 2015년에 징수되기 시작했습니다.                                                                                                                                     |
| mtaTax               | double    | 360         | 0.5 -0.5                                | 사용 중인 미터 요금에 따라 자동으로 활성화되는 $0.50 MTA 세금입니다.                                                                                                                                                                        |
| passengerCount       | int       | 64          | 1 2                                     | 차량의 승객 수입니다. 운전자가 입력한 값입니다.                                                                                                                                                                               |
| paymentType          | 문자열    | 6,282       | CSH CRD                                 | 승객이 이동 요금을 지불한 방식을 나타내는 숫자 코드입니다. 1 = 신용 카드, 2 = 현금, 3 = 무료, 4 = 분쟁, 5 = 알 수 없음, 6 = 이동 무효화                                                                                                          |
| puLocationId         | 문자열    | 266         | 237 161                                 | 택시미터가 작동된 TLC 택시 승차 구역입니다.                                                                                                                                                                                                      |
| puMonth              | int       | 12          | 3 5                                     |                                                                                                                                                                                                                                                        |
| puYear               | int       | 29          | 2012 2011                               |                                                                                                                                                                                                                                                        |
| rateCodeId           | int       | 56          | 1 2                                     | 이동 종료 시 유효한 최종 요금 코드입니다. 1= 표준 요금, 2= JFK, 3= Newark, 4= Nassau 또는 Westchester, 5= 협상된 요금, 6= 단체 탑승                                                                                                |
| startLat             | double    | 833,016     | 41.366138 40.7741                       |                                                                                                                                                                                                                                                        |
| startLon             | double    | 957,428     | -73.137393 -73.9821                     |                                                                                                                                                                                                                                                        |
| storeAndFwdFlag      | 문자열    | 8           | N 0                                     | 이 플래그는 차량이 서버에 연결되지 않았으므로 이동 레코드를 공급업체로 보내기 전에 이동 레코드가 차량 메모리에 보관(“저장 및 전달”이라고도 함)되었는지를 나타냅니다. Y= 이동 저장 및 전달, N= 이동 저장 및 전달 아님 |
| tipAmount            | double    | 12,121      | 1.0 2.0                                 | 이 필드는 신용 카드 팁을 위해 자동으로 채워집니다. 현금 팁은 포함되지 않습니다.                                                                                                                                                                |
| tollsAmount          | double    | 6,634       | 5.33 4.8                                | 이동 시 결제된 모든 요금의 총금액입니다.                                                                                                                                                                                                                |
| totalAmount          | double    | 39,707      | 7.0 7.8                                 | 승객에게 청구된 총금액입니다. 현금 팁은 포함되지 않습니다.                                                                                                                                                                                    |
| tpepDropoffDateTime  | timestamp | 290,185,010 | 2010-11-07 01:29:00 2013-11-03 01:22:00 | 미터가 해제된 날짜 및 시간입니다.                                                                                                                                                                                                       |
| tpepPickupDateTime   | timestamp | 289,948,585 | 2010-11-07 01:00:00 2009-11-01 01:05:00 | 미터가 작동된 날짜 및 시간입니다.                                                                                                                                                                                                          |
| tripDistance         | double    | 14,003      | 1.0 0.9                                 | 택시미터에서 보고된 경과한 이동 거리(마일)입니다.                                                                                                                                                                                          |
| vendorID             | 문자열    | 7           | VTS CMT                                 | 레코드를 제공한 TPEP 공급자를 나타내는 코드입니다. 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                   |
| vendorID             | int       | 2           | 2 1                                     | 레코드를 제공한 LPEP 공급자를 나타내는 코드입니다. 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                   |

## <a name="preview"></a>미리 보기

| vendorID | tpepPickupDateTime    | tpepDropoffDateTime   | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeId | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | puYear | puMonth |
|----------|-----------------------|-----------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|--------|---------|
| 2        | 1/24/2088 12:25:39 AM | 1/24/2088 7:28:25 AM  | 1              | 4.05         | 24           | 162          | 1          | N               | 2           | 14.5       | 0     | 0.5    | 0.3                  | 0         | 0           | 15.3        | 2088   | 1       |
| 2        | 1/24/2088 12:15:42 AM | 1/24/2088 12:19:46 AM | 1              | 0.63         | 41           | 166          | 1          | N               | 2           | 4.5.        | 0     | 0.5    | 0.3                  | 0         | 0           | 5.3         | 2088   | 1       |
| 2        | 11/4/2084 12:32:24 PM | 11/4/2084 12:47:41 PM | 1              | 1.34         | 238          | 236          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 12:25:53 PM | 11/4/2084 12:29:00 PM | 1              | 0.32         | 238          | 238          | 1          | N               | 2           | 4          | 0     | 0.5    | 0.3                  | 0         | 0           | 4.8         | 2084   | 11      |
| 2        | 11/4/2084 12:08:33 PM | 11/4/2084 12:22:24 PM | 1              | 1.85         | 236          | 238          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 11:41:35 AM | 11/4/2084 11:59:41 AM | 1              | 1.65         | 68           | 237          | 1          | N               | 2           | 12.5       | 0     | 0.5    | 0.3                  | 0         | 0           | 13.3        | 2084   | 11      |
| 2        | 11/4/2084 11:27:28 AM | 11/4/2084 11:39:52 AM | 1              | 1.07         | 170          | 68           | 1          | N               | 2           | 9          | 0     | 0.5    | 0.3                  | 0         | 0           | 9.8         | 2084   | 11      |
| 2        | 11/4/2084 11:19:06 AM | 11/4/2084 11:26:44 AM | 1              | 1.3          | 107          | 170          | 1          | N               | 2           | 7.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 8.3         | 2084   | 11      |
| 2        | 11/4/2084 11:02:59 AM | 11/4/2084 11:15:51 AM | 1              | 1.85         | 113          | 137          | 1          | N               | 2           | 10         | 0     | 0.5    | 0.3                  | 0         | 0           | 10.8        | 2084   | 11      |
| 2        | 11/4/2084 10:46:05 AM | 11/4/2084 10:50:09 AM | 1              | 0.62         | 231          | 231          | 1          | N               | 2           | 4.5.        | 0     | 0.5    | 0.3                  | 0         | 0           | 5.3         | 2084   | 11      |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_pandas_dataframe()

nyc_tlc_df.info()
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

```python
# Pip install packages
import os, sys

!{sys.executable} -m pip install azure-storage-blob
!{sys.executable} -m pip install pyarrow
!{sys.executable} -m pip install pandas

# Azure storage access info
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = r""
container_name = "nyctlc"
folder_name = "yellow"

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

# Read the parquet file into Pandas data frame
import pandas as pd

print('Reading the parquet file into Pandas data frame')
df = pd.read_parquet(filename)

# you can add your filter at below
print('Loaded as a Pandas data frame: ')
df
```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "yellow"
blob_sas_token = r"

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcYellow

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "yellow"
blob_sas_token = r""

# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)

# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')

# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.