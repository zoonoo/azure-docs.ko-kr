---
title: NYC 택시 및 리무진 녹색 데이터 세트
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 NYC 택시 및 리무진 녹색 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 058969168b09c1eb394b6fc7a9f06c401c7f9763
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039043"
---
# <a name="nyc-taxi--limousine-commission---green-taxi-trip-records"></a>NYC 택시 및 리무진 협회 - 녹색 택시 이동 레코드

녹색 택시 이동 레코드에는 승차 및 하차 날짜/시간, 승차 및 하단 위치, 이동 거리, 항목별 요금, 요율 종류, 지불 유형 및 운전자가 보고한 승객 수를 캡처하는 필드가 포함됩니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 Parquet 형식으로 저장됩니다. 2018년 기준 총 약 8천만 개의 행(2GB)이 있습니다.

이 데이터 세트는 2009년부터 2018년까지 누적된 기록 레코드를 포함합니다. SDK의 매개 변수 설정을 사용하여 특정 시간 범위의 데이터를 가져올 수 있습니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="additional-information"></a>추가 정보

NYC TLC(택시 및 리무진 위원회):

데이터는 TPEP/LPEP(Taxicab & Livery Passenger Enhancement Programs)에 따라 승인된 기술 공급 기업에 의해 수집되어 NYC TLC(택시 및 리무진 위원회)에 제공되었습니다. 이동 데이터는 TLC에서 만든 것이 아니며, TLC는 이 데이터의 정확성에 관해 어떠한 진술도 하지 않습니다.

[원래 데이터 세트 위치](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) 및 [원래 사용 약관](https://www1.nyc.gov/home/terms-of-use.page)을 확인합니다.

## <a name="columns"></a>열

| 이름                 | 데이터 형식 | 고유한     | 값(샘플)                         | Description                                                                                                                                                                                                                                          |
|----------------------|-----------|------------|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| doLocationId         | 문자열    | 264        | 74 42                                   | 택시미터가 해제된 DOLocationID TLC 택시 승차 구역입니다.                                                                                                                                                                                    |
| dropoffLatitude      | double    | 109,721    | 40.7743034362793 40.77431869506836      | 2016년 7월부터 계속 사용되지 않음                                                                                                                                                                                                                       |
| dropoffLongitude     | double    | 75,502     | -73.95272827148438 -73.95274353027344   | 2016년 7월부터 계속 사용되지 않음                                                                                                                                                                                                                       |
| extra                | double    | 202        | 0.5 1.0                                 | 기타 추가 비용 및 추가 요금입니다. 현재 혼잡 시간대 요금 $0.50 및 야간할증 요금 $1만 포함됩니다.                                                                                                                                 |
| fareAmount           | double    | 10,367     | 6.0 5.5                                 | 미터로 계산된 시간/거리 병산 요금입니다.                                                                                                                                                                                                  |
| improvementSurcharge | 문자열    | 92         | 0.3 0                                   | 차량 호출 기본요금에서 $0.30 개선 추가 요금이 평가되었습니다. 개선 추가 요금은 2015년에 징수되기 시작했습니다.                                                                                                                         |
| lpepDropoffDatetime  | timestamp | 58,100,713 | 2016-05-22 00:00:00 2016-05-09 00:00:00 | 미터가 해제된 날짜 및 시간입니다.                                                                                                                                                                                                     |
| lpepPickupDatetime   | timestamp | 58,157,349 | 2013-10-22 12:40:36 2014-08-09 15:54:25 | 미터가 작동된 날짜 및 시간입니다.                                                                                                                                                                                                        |
| mtaTax               | double    | 34         | 0.5 -0.5                                | 사용 중인 미터 요금에 따라 자동으로 활성화되는 $0.50 MTA 세금입니다.                                                                                                                                                                      |
| passengerCount       | int       | 10         | 1 2                                     | 차량의 승객 수입니다. 운전자가 입력한 값입니다.                                                                                                                                                                             |
| paymentType          | int       | 5          | 2 1                                     | 승객이 이동 요금을 지불한 방식을 나타내는 숫자 코드입니다. 1 = 신용 카드 2 = 현금 3 = 무료 4 = 분쟁 5 = 알 수 없음 6 = 이동 무효화                                                                                                              |
| pickupLatitude       | double    | 95,110     | 40.721351623535156 40.721336364746094   | 2016년 7월부터 계속 사용되지 않음                                                                                                                                                                                                                       |
| pickupLongitude      | double    | 55,722     | -73.84429931640625 -73.84429168701172   | 2016년 7월부터 계속 사용되지 않음                                                                                                                                                                                                                       |
| puLocationId         | 문자열    | 264        | 74 41                                   | 택시미터가 작동된 TLC 택시 승차 구역입니다.                                                                                                                                                                                                    |
| puMonth              | int       | 12         | 3 5                                     |                                                                                                                                                                                                                                                      |
| puYear               | int       | 14         | 2015 2016                               |                                                                                                                                                                                                                                                      |
| rateCodeID           | int       | 7          | 1 5                                     | 이동 종료 시 유효한 최종 요금 코드입니다. 1= 표준 요금 2= JFK 3= Newark 4= Nassau 또는 Westchester 5= 협상된 요금 6= 단체 탑승                                                                                                    |
| storeAndFwdFlag      | 문자열    | 2          | N Y                                     | 이 플래그는 차량이 서버에 연결되지 않았으므로 이동 레코드를 공급업체로 보내기 전에 이동 레코드가 차량 메모리에 보관(“저장 및 전달”이라고도 함)되었는지를 나타냅니다. Y= 이동 저장 및 전달 N= 이동 저장 및 전달 아님 |
| tipAmount            | double    | 6,206      | 1.0 2.0                                 | 팁 금액 – 이 필드는 신용 카드 팁을 위해 자동으로 채워집니다. 현금 팁은 포함되지 않습니다.                                                                                                                                                 |
| tollsAmount          | double    | 2,150      | 5.54 5.76                               | 이동 시 결제된 모든 요금의 총금액입니다.                                                                                                                                                                                                              |
| totalAmount          | double    | 20,188     | 7.8 6.8                                 | 승객에게 청구된 총금액입니다. 현금 팁은 포함되지 않습니다.                                                                                                                                                                                  |
| tripDistance         | double    | 7,060      | 0.9 1.0                                 | 택시미터에서 보고된 경과한 이동 거리(마일)입니다.                                                                                                                                                                                        |
| tripType             | int       | 3          | 1 2                                     | 승객이 거리에서 택시를 잡았는지 또는 사용 중인 미터 요금에 따라 자동으로 할당되었지만 운전자가 변경할 수 있는 배차인지를 나타내는 코드입니다. 1= 거리에서 택시를 잡음 2= 배차                                                      |
| vendorID             | int       | 2          | 2 1                                     | 레코드를 제공한 LPEP 공급자를 나타내는 코드입니다. 1= Creative Mobile Technologies, LLC; 2= VeriFone Inc.                                                                                                                                 |

## <a name="preview"></a>미리 보기

| vendorID | lpepPickupDatetime     | lpepDropoffDatetime    | passengerCount | tripDistance | puLocationId | doLocationId | rateCodeID | storeAndFwdFlag | paymentType | fareAmount | extra | mtaTax | improvementSurcharge | tipAmount | tollsAmount | totalAmount | tripType | puYear | puMonth |
|----------|------------------------|------------------------|----------------|--------------|--------------|--------------|------------|-----------------|-------------|------------|-------|--------|----------------------|-----------|-------------|-------------|----------|--------|---------|
| 2        | 6/24/2081 5:40:37 PM   | 6/24/2081 6:42:47 PM   | 1              | 16.95        | 93           | 117          | 1          | N               | 1           | 52         | 1     | 0.5    | 0.3                  | 0         | 2.16        | 55.96       | 1        | 2081   | 6       |
| 2        | 11/28/2030 12:19:29 AM | 11/28/2030 12:25:37 AM | 1              | 1.08         | 42           | 247          | 1          | N               | 2           | 6.5        | 0     | 0.5    | 0.3                  | 0         | 0           | 7.3         | 1        | 2030   | 11      |
| 2        | 11/28/2030 12:14:50 AM | 11/28/2030 12:14:54 AM | 1              | 0.03         | 42           | 42           | 5          | N               | 2           | 5          | 0     | 0      | 0                    | 0         | 0           | 5           | 2        | 2030   | 11      |
| 2        | 11/14/2020 11:38:07 AM | 11/14/2020 11:42:22 AM | 1              | 0.63         | 129          | 129          | 1          | N               | 2           | 4.5.        | 1     | 0.5    | 0.3                  | 0         | 0           | 6.3         | 1        | 2020   | 11      |
| 2        | 11/14/2020 9:55:36 AM  | 11/14/2020 10:04:54 AM | 1              | 3.8          | 82           | 138          | 1          | N               | 2           | 12.5       | 1     | 0.5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2020   | 11      |
| 2        | 8/26/2019 4:18:37 PM   | 8/26/2019 4:19:35 PM   | 1              | 0            | 264          | 264          | 1          | N               | 2           | 1          | 0     | 0.5    | 0.3                  | 0         | 0           | 1.8         | 1        | 2019   | 8       |
| 2        | 7/1/2019 8:28:33 AM    | 7/1/2019 8:32:33 AM    | 1              | 0.71         | 7            | 7            | 1          | N               | 1           | 5          | 0     | 0.5    | 0.3                  | 1.74      | 0           | 7.54        | 1        | 2019   | 7       |
| 2        | 7/1/2019 12:04:53 AM   | 7/1/2019 12:21:56 AM   | 1              | 2.71         | 223          | 145          | 1          | N               | 2           | 13         | 0.5   | 0.5    | 0.3                  | 0         | 0           | 14.3        | 1        | 2019   | 7       |
| 2        | 7/1/2019 12:04:11 AM   | 7/1/2019 12:21:15 AM   | 1              | 3.14         | 166          | 142          | 1          | N               | 2           | 14.5       | 0.5   | 0.5    | 0.3                  | 0         | 0           | 18.55       | 1        | 2019   | 7       |
| 2        | 7/1/2019 12:03:37 AM   | 7/1/2019 12:09:27 AM   | 1              | 0.78         | 74           | 74           | 1          | N               | 1           | 6          | 0.5   | 0.5    | 0.3                  | 1.46      | 0           | 8.76        | 1        | 2019   | 7       |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
folder_name = "green"

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
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
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
blob_relative_path = "green"
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

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

```python
# This is a package in preview.
from azureml.opendatasets import NycTlcGreen

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcGreen(start_date=start_date, end_date=end_date)
nyc_tlc_df = nyc_tlc.to_spark_dataframe()

# Display top 5 rows
display(nyc_tlc_df.limit(5))

# Display data statistic information
display(nyc_tlc_df, summary = True)
```

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "green"
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