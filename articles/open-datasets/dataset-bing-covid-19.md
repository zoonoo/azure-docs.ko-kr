---
title: Bing 코로나19
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 Bing 코로나19 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 7d797e169a0f1fbeeceaf377e731a051112c68e3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452302"
---
# <a name="bing-covid-19"></a>Bing 코로나19

Bing 코로나19 데이터에는 모든 지역의 확진, 사망 및 완치 사례가 포함되며, 이 데이터는 매일 업데이트됩니다.
이 데이터는 [Bing 코로나19 추적기](https://bing.com/covid)에 반영됩니다.

Bing은 [WHO(세계 보건 기구)](https://www.who.int/emergencies/diseases/novel-coronavirus-2019), [CDC(미국 질병통제예방센터)](https://www.cdc.gov/coronavirus/2019-ncov/index.html), 국가 및 주 공중 위생 관련 부서, [BNO News](https://bnonews.com/index.php/2020/04/the-latest-coronavirus-cases/), [24/7 Wall St.](https://247wallst.com/), [Wikipedia](https://en.wikipedia.org/wiki/2019%E2%80%9320_coronavirus_pandemic) 등의 신뢰할 수 있는 여러 출처의 데이터를 수집합니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>데이터 세트
수정된 데이터 세트는 CSV, JSON, JSON-Lines 및 Parquet 형식으로 사용 가능합니다.

- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

모든 수정된 데이터 세트는 ISO 3166 하위 코드와 추가된 로드 시간이 있고, 밑줄 구분 기호가 있는 소문자 형식의 열 이름을 사용합니다.

원시 데이터: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/bing_covid-19_data/latest/Bing-COVID19-Data.csv

수정된 원시 데이터의 이전 버전: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/

## <a name="data-volume"></a>데이터 볼륨
모든 데이터 세트는 매일 업데이트됩니다. 2020년 5월 11일 기준, 125,576개 행을 포함합니다(CSV 16.1MB, JSON 40.0MB, JSONL 39.6MB, Parquet 1.1MB).

## <a name="license-and-use-rights-attribution"></a>라이선스 및 사용 권한 표시
이 데이터는 [사용 약관](https://github.com/microsoft/Bing-COVID-19-Data/blob/master/LICENSE.txt)에 따라 의학 연구, 정부 기관, 교육 기관과 같은 교육 및 학문적 목적으로만 사용할 수 있습니다.

게시물에 사용되거나 인용된 데이터는 ‘Bing 코로나19 추적기’에 대한 표시와 함께 www.bing.com/covid 링크를 포함해야 합니다.

## <a name="contact"></a>연락처
이 데이터 세트나 코로나19 데이터 레이크의 다른 데이터 세트에 대한 질문이나 피드백이 있는 경우 askcovid19dl@microsoft.com으로 문의하세요.

## <a name="columns"></a>열

| 이름             | 데이터 형식 | 고유한    | 값(샘플)                    | Description                                                          |
|------------------|-----------|-----------|------------------------------------|----------------------------------------------------------------------|
| admin_region_1   | 문자열    | 864       | Texas Georgia                      | country_region 내의 지역                                         |
| admin_region_2   | 문자열    | 3,143     | Washington County Jefferson County | admin_region_1 내의 지역                                         |
| confirmed        | int       | 120,692   | 1 2                                | 지역의 확진 사례 수치                                  |
| confirmed_change | int       | 12,120    | 1 2                                | 전날에서의 확진 사례 수치 변화                 |
| country_region   | 문자열    | 237       | United States India                | 국가/지역                                                       |
| deaths           | int       | 20,616    | 1 2                                | 지역의 사망 사례 수치                                      |
| deaths_change    | smallint  | 1,981     | 1 2                                | 전날에서의 사망 수치 변화                          |
| id               | int       | 1,783,534 | 742546 69019298                    | 고유 식별자                                                    |
| iso_subdivision  | 문자열    | 484       | US-TX US-GA                        | 두 부분으로 구성된 ISO 하위 코드                                        |
| iso2             | 문자열    | 226       | US IN                              | 2자로 된 국가 코드 식별자                                     |
| iso3             | 문자열    | 226       | USA IND                            | 3자로 된 국가 코드 식별자                                     |
| latitude         | double    | 5,675     | 42.28708 19.59852                  | 지역의 중심 위도                               |
| load_time        | timestamp | 1         | 2021-04-26 00:06:34.719000         | GitHub에서 Bing 원본으로부터 파일이 로드된 날짜 및 시간 |
| longitude        | double    | 5,693     | -2.5396 -155.5186                  | 지역의 중심 경도                              |
| recovered        | int       | 73,287    | 1 2                                | 지역의 완치 수치                                       |
| recovered_change | int       | 10,441    | 1 2                                | 전날에서의 완치 사례 수치 변화                 |
| 업데이트됨          | date      | 457       | 2021-04-23 2021-04-22              | 레코드의 기준 날짜                                        |

## <a name="preview"></a>미리 보기

| id     | 업데이트됨    | confirmed | deaths | iso2 | iso3 | country_region | admin_region_1 | iso_subdivision | admin_region_2 | load_time             | confirmed_change | deaths_change |
|--------|------------|-----------|--------|------|------|----------------|----------------|-----------------|----------------|-----------------------|------------------|---------------|
| 338995 | 2020-01-21 | 262       | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM |                  |               |
| 338996 | 2020-01-22 | 313       | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 51               | 0             |
| 338997 | 2020-01-23 | 578       | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 265              | 0             |
| 338998 | 2020-01-24 | 841       | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 263              | 0             |
| 338999 | 2020-01-25 | 1320      | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 479              | 0             |
| 339000 | 2020-01-26 | 2014      | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 694              | 0             |
| 339001 | 2020-01-27 | 2798      | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 784              | 0             |
| 339002 | 2020-01-28 | 4593      | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 1795             | 0             |
| 339003 | 2020-01-29 | 6065      | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 1472             | 0             |
| 339004 | 2020-01-30 | 7818      | 0      | null | null | 전 세계      | null           | null            | null           | 4/26/2021 12:06:34 AM | 1753             | 0             |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=bing-covid-19-data)** .

#### <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-bing-covid-19-dataset"></a>이 Notebook은 [Bing 코로나19 데이터 세트](https://github.com/microsoft/Bing-COVID-19-Data)에 액세스하기 위한 URL과 샘플 코드를 문서화합니다.

다음 URL을 사용하여 Azure Blob Storage에서 호스트되는 특정 파일 형식을 가져옵니다.

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet

Pandas의 기본 제공 기능인 http URL에서 다운로드를 사용하여 데이터 세트 파일을 다운로드합니다. Pandas에는 다양한 파일 형식을 위한 읽기 프로그램이 있습니다.

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet")
df.head(10)
```

다양한 필드의 데이터 형식을 확인하고 업데이트된 열이 datetime 형식인지 확인하겠습니다.

```python
df.dtypes
```

이제 전 세계 데이터를 살펴보고 데이터를 시각화하는 몇 가지 간단한 차트를 그립니다.

```python
df_Worldwide=df[df['country_region']=='Worldwide']
```

```python
df_Worldwide_pivot=df_Worldwide.pivot_table(df_Worldwide, index=['country_region','updated'])

df_Worldwide_pivot
```

```python
df_Worldwide.plot(kind='line',x='updated',y="confirmed",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="confirmed_change",grid=True)
df_Worldwide.plot(kind='line',x='updated',y="deaths_change",grid=True)
```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
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

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data -->

> [!TIP]
> **[대신 Notebook을 다운로드하세요](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=bing-covid-19-data)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/bing_covid-19_data/latest/bing_covid-19_data.parquet"
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
