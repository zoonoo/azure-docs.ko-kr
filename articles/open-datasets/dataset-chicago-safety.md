---
title: 시카고 안전 데이터
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 시카고 안전 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: d39c89308f0c33f98f1c5d074746a2008317472a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039118"
---
# <a name="chicago-safety-data"></a>시카고 안전 데이터

시카고시의 311 서비스 요청으로, 위생 관리 코드 불만 사항 기록, 보고된 포트홀 및 가로등 문제를 포함합니다.

모든 미해결 위생 관리 코드 불만 사항이 311에 보고되었으며 모든 요청은 2011년 1월 1일 이후 완료되었습니다. Department of Streets and Sanitation은 보고된 시카고의 위생 관리 코드 위반 사항을 조사하고 해결합니다. 거주자는 넘쳐흐르는 골목의 쓰레기통과 쓰레기 같은 위반 사항에 대해 서비스를 요청할 수 있습니다. 311은 중복된 위생 관리 코드 불만 사항을 접수하는 경우도 있습니다. 중복으로 레이블이 지정된 요청은 이전 요청과 동일한 지역에 속하고 311의 CSR(고객 서비스 요청) 시스템에 거의 동시에 입력되었습니다. 중복된 불만 사항은 상태 필드에 “Open - Dup”(미해결 - 중복) 또는 “Completed - Dup”(완료됨 - 중복)과 같은 레이블이 지정됩니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

CDOT(Chicago Department of Transportation)는 시카고의 6,437km(4,000마일)이 넘는 간선도로 및 주택가에 있는 포트홀 보수 작업을 감독합니다. CDOT는 311 콜 센터를 통해 포트홀의 보고서를 받습니다. CDOT는 매핑 및 추적 시스템을 사용하여 포트홀 위치를 파악한 후 담당자의 일정을 잡습니다.

311 통화 한 건으로 여러 건의 포트홀 복구가 생성될 수 있습니다. 담당자가 도착하여 311 포트홀을 복구하는 경우 블록 내에 있는 다른 모든 포트홀도 메우게 됩니다. 포트홀 복구는 포트홀이 311에 처음 보고된 날로부터 7일 이내에 완료됩니다. 기상 상태, 몹시 낮은 기온 및 강수량은 복구 기간에 영향을 줍니다. 기상 상태가 좋고 비가 오지 않는 날 담당자는 수천 개의 포트홀을 메울 수 있습니다. 

이전 요청이 4개의 주소 버퍼에 대해 이미 열려 있으면 해당 요청에는 “Duplicate (Open)”(중복(미해결)) 상태가 지정됩니다. 예를 들어 6535 N Western 지역에 대한 기존 CSR이 있는데 6531 N Western 지역(원본 CSR의 4개 주소에 포함)에 대해 새 요청이 접수되면 새 요청에는 “Duplicate (Open)”(중복(미해결)) 상태가 지정됩니다. 도로가 복구되면 CSR의 상태는 원본 요청에 대해 “Completed”(완료됨)로 표시되고 중복된 요청에 대해서는 “Duplicate (Closed)”(중복(종결됨))로 표시됩니다. 또한 보고된 주소를 조사했지만 포트홀이 발견되지 않거나 이미 메워진 경우 서비스 요청은 “Completed”(완료됨) 상태를 수신합니다. “붕괴”, “설비 파손” 등의 다른 문제가 도로에서 발견되는 경우 관련 부서나 수급자에게 전달됩니다.

모든 미해결 “Street Lights - All Out”(가로등 - 모두 정전)(3개 이상의 가로등 정전)이 311에 보고되었으며 모든 요청은 2011년 1월 1일 이후 완료되었습니다. CDOT(Chicago Department of Transportation)는 시카고의 간선도로 및 주택가를 비추는 약 25만 개의 가로등을 관리 감독합니다. CDOT는 거주자의 가로등 정전 보고에 대한 응답으로 복구 및 전구 교체 작업을 수행합니다. CDOT에서 “All Out”(모두 정전)이라는 보고를 받으면 복구하도록 배정된 전기 기사가 해당 회로(각 회로에 8-16개의 가로등이 있음)에 있는 가로등을 점검하고 모두 제대로 작동하는지 확인합니다. 원본 요청일로부터 4일 이내에 동일한 회로의 가로등 정전이라는 두 번째 요청이 접수되면 최신 요청에 “Duplicate (Open)”(중복(미해결)) 상태가 자동으로 지정됩니다. CDOT의 전기 기사가 회로의 가로등을 점검하여 모두 작동함을 확인했으므로 모든 “Duplicate (Open)”(중복(미해결)) 주소는 자동으로 관찰되고 복구됩니다. 가로등이 복구되면 CSR의 상태는 원본 요청에 대해 “Completed”(완료됨)로 표시되고 중복된 요청에 대해서는 “Duplicate (Closed)”(중복(종결됨))로 표시됩니다. 또한 보고된 가로등을 조사했는데 수리가 잘되어 작동하는 것으로 확인되거나, 존재하지 않는 주소에 대한 서비스 요청이거나, 가로등이 수급자에 의해 유지 관리되는 경우 서비스 요청은 “Completed”(완료됨) 상태를 수신합니다. 데이터는 매일 업데이트됩니다.

## <a name="volume-and-retention"></a>볼륨 및 보존

이 데이터 세트는 Parquet 형식으로 저장됩니다. 이 데이터 세트는 매일 업데이트되며, 2018년 기준 총 약 1백만 개의 행(80MB)을 포함합니다.

이 데이터 세트는 2011년부터 2018년까지 누적된 기록 레코드를 포함합니다. SDK의 매개 변수 설정을 사용하여 특정 시간 범위의 데이터를 가져올 수 있습니다.

## <a name="storage-location"></a>스토리지 위치

이 데이터 세트는 미국 동부 Azure 지역에 저장됩니다. 선호도를 위해 미국 동부에 컴퓨팅 리소스를 할당하는 것이 좋습니다.

## <a name="related-datasets"></a>관련 데이터 세트

- [시카고 위생](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Sanitation-Code-Complaints-Hi/me59-5fac)
- [시카고 포트홀](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Pot-Holes-Reported-Historical/7as2-ds3y)
- [시카고 가로등](https://data.cityofchicago.org/Service-Requests/311-Service-Requests-Street-Lights-All-Out-Histori/zuxi-7xem)

## <a name="additional-information"></a>추가 정보

이 데이터 세트는 시카고시 정부에서 제공됩니다.

이 데이터 세트 사용과 관련된 사용 약관은 여기를 참조하세요. 데이터 원본에 대한 질문이 있는 경우 dataportal@cityofchicago.org으로 문의해 주세요.

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

| dataType | dataSubtype | dateTime | category | subcategory | 상태 | address | latitude | longitude | source | extendedProperties |
|-|-|-|-|-|-|-|-|-|-|-|
| 안전 | 311_All | 4/25/2021 11:55:04 PM | Street Light Out Complaint | null | 열기 | 4800 W WASHINGTON BLVD | 41.882148426 | -87.74556256 | null |  |
| 안전 | 311_All | 4/25/2021 11:54:31 PM | 311 INFORMATION ONLY CALL | null | 완료됨 | 2111 W Lexington ST |  |  | null |  |
| 안전 | 311_All | 4/25/2021 11:52:11 PM | 311 INFORMATION ONLY CALL | null | 완료됨 | 2111 W Lexington ST |  |  | null |  |
| 안전 | 311_All | 4/25/2021 11:49:56 PM | 311 INFORMATION ONLY CALL | null | 완료됨 | 2111 W Lexington ST |  |  | null |  |
| 안전 | 311_All | 4/25/2021 11:48:53 PM | Garbage Cart Maintenance | null | 열기 | 3409 E 106TH ST | 41.702545562 | -87.540917602 | null |  |
| 안전 | 311_All | 4/25/2021 11:46:01 PM | 311 INFORMATION ONLY CALL | null | 완료됨 | 2111 W Lexington ST |  |  | null |  |
| 안전 | 311_All | 4/25/2021 11:45:46 PM | Aircraft Noise Complaint | null | 완료됨 | 10510 W ZEMKE RD |  |  | null |  |
| 안전 | 311_All | 4/25/2021 11:45:02 PM | 311 INFORMATION ONLY CALL | null | 완료됨 | 2111 W Lexington ST |  |  | null |  |
| 안전 | 311_All | 4/25/2021 11:44:24 PM | Sewer Cave-In Inspection Request | null | 열기 | 7246 W THORNDALE AVE | 41.987984339 | -87.808702917 | null |  |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_pandas_dataframe()
```

```
safety.info()
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=city_safety_chicago)** .

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
folder_name = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```
# This is a package in preview.
# You need to pip install azureml-opendatasets in Databricks cluster. https://docs.microsoft.com/en-us/azure/data-explorer/connect-from-databricks#install-the-python-library-on-your-azure-databricks-cluster
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser


end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```
display(safety.limit(5))
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=city_safety_chicago)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=azureml-opendatasets&registryId=city_safety_chicago)** .

```python
# This is a package in preview.
from azureml.opendatasets import ChicagoSafety

from datetime import datetime
from dateutil import parser

end_date = parser.parse('2016-01-01')
start_date = parser.parse('2015-05-01')
safety = ChicagoSafety(start_date=start_date, end_date=end_date)
safety = safety.to_spark_dataframe()
```

```python
# Display top 5 rows
display(safety.limit(5))
```

```python
# Display data statistic information
display(safety, summary = True)
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=city_safety_chicago)** .

```python
# Azure storage access info
blob_account_name = "azureopendatastorage"
blob_container_name = "citydatacontainer"
blob_relative_path = "Safety/Release/city=Chicago"
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