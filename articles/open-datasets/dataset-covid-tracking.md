---
title: 코로나 추적 프로젝트
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 코로나 추적 프로젝트 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 55814a6b1b78673c20447d6129f609058d5c794f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453705"
---
# <a name="covid-tracking-project"></a>코로나 추적 프로젝트

COVID Tracking Project 데이터 세트는 미국 전 지역 및 영토의 최신 검사 수, 확인된 사례, 입원, 환자 결과를 제공합니다.

이 데이터 세트에 대한 자세한 내용은 [프로젝트 GitHub 리포지토리](https://github.com/COVID19Tracking/covid-tracking-data)를 참조하세요.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>데이터 세트

수정된 버전의 데이터 세트는 CSV, JSON, JSON-Lines 및 Parquet 형식으로 사용 가능합니다.
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

모든 수정된 버전은 ISO 3166 하위 코드와 추가된 로드 시간이 있고, 밑줄 구분 기호가 있는 소문자 형식의 열 이름을 사용합니다.

원시 데이터: 'https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/latest/daily.json '

수정된 원시 데이터의 이전 버전: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/

https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_tracking/

## <a name="data-volume"></a>데이터 볼륨
모든 데이터 세트는 매일 업데이트됩니다. 2020년 5월 13일 기준, 4,100개의 행을 포함합니다(CSV 574KB, JSON 1.8MB, JSONL 1.8MB, Parquet 334KB).

## <a name="data-source"></a>데이터 원본

이 데이터는 원래 Atlantic의 COVID Tracking Project에 의해 게시되었습니다. 원시 데이터는 [states_daily_4p_et.csv 파일](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/data/states_daily_4pm_et.csv)을 사용하여 코로나 추적 GitHub 리포지토리에서 수집됩니다. 코로나 추적 프로젝트 API의 원본을 포함하여 이 데이터 세트에 대한 자세한 내용은 [프로젝트 GitHub 리포지토리](https://github.com/COVID19Tracking/covid-tracking-data)를 참조하세요.

## <a name="data-quality"></a>데이터 품질
코로나 추적 프로젝트는 각 주의 데이터 품질을 분류하고 데이터 품질 평가에 대한 추가 정보를 제공합니다. 자세한 내용은 [코로나 추적 프로젝트 데이터 페이지](https://covidtracking.com/data)를 참조하세요. GitHub 리포지토리의 데이터는 API보다 1시간 뒤의 데이터일 수 있습니다. 최신 데이터에 액세스하려면 API를 사용해야 합니다.

## <a name="license-and-use-rights-attribution"></a>라이선스 및 사용 권한 특성

이 데이터는 [Apache 라이선스 2.0](https://github.com/COVID19Tracking/covid-tracking-data/blob/master/LICENSE)의 사용 약관에 따라 사용이 허가됩니다.

이 데이터를 사용하는 모든 경우에 모든 저작권, 특허권, 상표 및 귀속 고지 사항을 유지해야 합니다.

## <a name="contact"></a>연락처

코로나19 Data Lake의 이 데이터 세트 또는 기타 데이터 세트에 대한 질문이나 피드백은 askcovid19dl@microsoft.com에 문의하세요.

## <a name="columns"></a>열

| 이름                        | 데이터 형식 | 고유한 | 값(샘플)                                                                   | Description                                                                                                                 |
|-----------------------------|-----------|--------|-----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| date                        | date      | 420    | 2020-11-10 2021-01-30                                                             | 일별 합계가 수집된 날짜입니다.                                                                             |
| date_checked                | 문자열    | 9,487  | 2020-12-01T00:00:00Z 2020-09-01T00:00:00Z                                         | 사용되지 않음                                                                                                                  |
| 죽음                       | smallint  | 7,327  | 2 5                                                                               | 지금까지 코로나19로 인해 사망한 사람의 총수입니다.                                                        |
| death_increase              | smallint  | 429    | 1 2                                                                               | 사용되지 않음                                                                                                                  |
| fips                        | smallint  | 56     | 26 55                                                                             | 해당 주의 인구 조사 FIPS 코드입니다.                                                                                             |
| fips_code                   | 문자열    | 60     | 53 25                                                                             | 해당 주의 인구 조사 FIPS 코드입니다.                                                                                             |
| hash                        | 문자열    | 20,780 | 63df8cccd23a5476bab2d8111b138e4c9becd35e c606cd6990f16086b5382e12d84f6206172d493d | 이 레코드의 해시                                                                                                      |
| 입원                | int       | 7,641  | 89995 4                                                                           | 사용되지 않음                                                                                                                  |
| hospitalized_cumulative     | int       | 7,641  | 89995 4                                                                           | 지금까지 코로나19로 인해 병원에 간 사람의 총수입니다(이후 완치되었거나 사망한 사람 포함). |
| hospitalized_currently      | smallint  | 3,886  | 8 13                                                                              | 해당 일에 코로나19로 인해 병원에 있었던 사람의 수입니다.                                                                      |
| hospitalized_increase       | smallint  | 615    | 1 2                                                                               | 사용되지 않음                                                                                                                  |
| in_icu_cumulative           | smallint  | 2,295  | 990 220                                                                           | 지금까지 코로나19로 인해 중환자실에 간 사람의 총수입니다(이후 완치되었거나 사망한 사람 포함).      |
| in_icu_currently            | smallint  | 1,643  | 2 8                                                                               | 해당 일에 코로나19로 인해 중환자실에 있었던 사람의 총수입니다.                                                                 |
| iso_country                 | 문자열    | 1      | US                                                                                | ISO 3166 국가 또는 지역 코드                                                                                             |
| iso_subdivision             | 문자열    | 57     | US-UM US-WA                                                                       | ISO 3166 하위 코드                                                                                                   |
| last_update_et              | timestamp | 9,487  | 2020-12-01 00:00:00 2020-09-01 00:00:00                                           | 해당 일에 마지막으로 데이터가 업데이트된 시간                                                                                        |
| load_time                   | timestamp | 1      | 2021-04-26 00:06:49.883000                                                        | 원본에서 Azure로 데이터가 로드된 날짜 및 시간                                                                  |
| 부정                    | int       | 10,864 | 305972 2140                                                                       | 지금까지 코로나19 검사 결과 음성으로 나온 사람의 총수입니다.                                                        |
| negative_increase           | int       | 7,328  | 6 17                                                                              | 사용되지 않음                                                                                                                  |
| on_ventilator_cumulative    | smallint  | 677    | 411 412                                                                           | 지금까지 코로나19로 인해 산소 호흡기를 사용한 사람의 총수입니다(이후 완치되었거나 사망한 사람 포함).    |
| on_ventilator_currently     | smallint  | 837    | 4 10                                                                              | 해당 일에 코로나 19로 인해 산소 호흡기를 사용한 사람의 수입니다.                                                               |
| 보류 중                     | smallint  | 944    | 2 17                                                                              | 아직 결과가 확정되지 않은 검사 수입니다.                                                                    |
| pos_neg                     | int       | 18,282 | 2140 2                                                                            | 사용되지 않음                                                                                                                  |
| 긍정                    | int       | 16,837 | 2 1                                                                               | 지금까지 코로나19 검사 결과 양성으로 나온 사람의 총수입니다.                                                        |
| positive_increase           | smallint  | 4,754  | 1 2                                                                               | 사용되지 않음                                                                                                                  |
| 복구됨                   | int       | 8,286  | 29 19                                                                             | 지금까지 코로나19에서 완치된 사람의 총수입니다.                                                             |
| state                       | 문자열    | 56     | MI PA                                                                             | 해당 주의 2자로 된 코드입니다.                                                                                              |
| total                       | int       | 18,283 | 2140 2                                                                            | 사용되지 않음                                                                                                                  |
| total_test_results          | int       | 18,648 | 2140 3                                                                            | 해당 주에서 제공한 총 검사 결과 수                                                                                    |
| total_test_results_increase | int       | 13,463 | 1 2                                                                               | 사용되지 않음                                                                                                                  |

## <a name="preview"></a>미리 보기

| date       | state | 긍정 | hospitalized_currently | hospitalized_cumulative | on_ventilator_currently | data_quality_grade | last_update_et        | hash                                     | date_checked          | 죽음 | 입원 | total   | total_test_results | pos_neg | fips | death_increase | hospitalized_increase | negative_increase | positive_increase | total_test_results_increase | fips_code | iso_subdivision | load_time             | iso_country | 부정 | in_icu_cumulative | on_ventilator_cumulative | 복구됨 | in_icu_currently |
|------------|-------|----------|------------------------|-------------------------|-------------------------|--------------------|-----------------------|------------------------------------------|-----------------------|-------|--------------|---------|--------------------|---------|------|----------------|-----------------------|-------------------|-------------------|-----------------------------|-----------|-----------------|-----------------------|-------------|----------|-------------------|--------------------------|-----------|------------------|
| 2021-03-07 | AK    | 56886    | 33                     | 1293                    | 2                       | null               | 2021/3/5 오전 3:59:00   | dc4bccd4bb885349d7e94d6fed058e285d4be164 | 2021/3/5 오전 3:59:00   | 305   | 1293         | 56886   | 1731628            | 56886   | 2    | 0              | 0                     | 0                 | 0                 | 0                           | 2         | US-AK           | 2021/4/26 오전 12:06:49 | US          |          |                   |                          |           |                  |
| 2021-03-07 | AL    | 499819   | 494                    | 45976                   |                         | null               | 2021/3/7 오전 11:00:00  | 997207b430824ea40b8eb8506c19a93e07bc972e | 2021/3/7 오전 11:00:00  | 10148 | 45976        | 2431530 | 2323788            | 2431530 | 1    | -1             | 0                     | 2087              | 408               | 2347                        | 1         | US-AL           | 2021/4/26 오전 12:06:49 | US          | 1931711  | 2676              | 1515                     | 295690    |                  |
| 2021-03-07 | AR    | 324818   | 335                    | 14926                   | 65                      | null               | 2021/3/7 오전 12:00:00  | 50921aeefba3e30d31623aa495b47fb2ecc72fae | 2021/3/7 오전 12:00:00  | 5319  | 14926        | 2805534 | 2736442            | 2805534 | 5    | 22             | 11                    | 3267              | 165               | 3380                        | 5         | US-AR           | 2021/4/26 오전 12:06:49 | US          | 2480716  |                   | 1533                     | 315517    | 141              |
| 2021-03-07 | AS    | 0        |                        |                         |                         | null               | 2020/12/1 오전 12:00:00 | 96d23f888c995b9a7f3b4b864de6414f45c728ff | 2020/12/1 오전 12:00:00 | 0     |              | 2140    | 2140               | 2140    | 60   | 0              | 0                     | 0                 | 0                 | 0                           | 60        | US-AS           | 2021/4/26 오전 12:06:49 | US          | 2140     |                   |                          |           |                  |
| 2021-03-07 | AZ    | 826454   | 963                    | 57907                   | 143                     | null               | 2021/3/7 오전 12:00:00  | 0437a7a96f4471666f775e63e86923eb5cbd8cdf | 2021/3/7 오전 12:00:00  | 16328 | 57907        | 3899464 | 7908105            | 3899464 | 4    | 5              | 44                    | 13678             | 1335              | 45110                       | 4         | US-AZ           | 2021/4/26 오전 12:06:49 | US          | 3073010  |                   |                          |           | 273              |
| 2021-03-07 | CA    | 3501394  | 4291                   |                         |                         | null               | 2021/3/7 오전 2:59:00   | 63c5c0fd2daef2fb65150e9db486de98ed3f7b72 | 2021/3/7 오전 2:59:00   |       |              | 3501394 | 49646014           | 3501394 | 6    | 258            | 0                     | 0                 | 3816              | 133186                      | 6         | US-CA           | 2021/4/26 오전 12:06:49 | US          |          |                   |                          |           | 1159             |
| 2021-03-07 | CO    | 436602   | 326                    | 23904                   |                         | null               | 2021/3/7 오전 1:59:00   | 444746cda3a596f183f3fa3269c8cab68704e819 | 2021/3/7 오전 1:59:00   | 5989  | 23904        | 2636060 | 6415123            | 2636060 | 8    | 3              | 18                    | 0                 | 840               | 38163                       | 8         | US-CO           | 2021/4/26 오전 12:06:49 | US          | 2199458  |                   |                          |           |                  |
| 2021-03-07 | CT    | 285330   | 428                    | 12257                   |                         | null               | 2021/3/4 오후 11:59:00  | bcc0f7bc8c2bf77eec31b25f8b59d510f679d3e7 | 2021/3/4 오후 11:59:00  | 7704  | 12257        | 285330  | 6520366            | 285330  | 9    | 0              | 0                     | 0                 | 0                 | 0                           | 9         | US-CT           | 2021/4/26 오전 12:06:49 | US          |          |                   |                          |           |                  |
| 2021-03-07 | DC    | 41419    | 150                    |                         | 16                      | null               | 2021/3/6 오전 12:00:00  | a3aa0d623d538807fb9577ad64354f48cf728cc8 | 2021/3/6 오전 12:00:00  | 1030  |              | 41419   | 1261363            | 41419   | 11   | 0              | 0                     | 0                 | 146               | 5726                        | 11        | US-DC           | 2021/4/26 오전 12:06:49 | US          |          |                   |                          | 29570     | 38               |
| 2021-03-07 | DE    | 88354    | 104                    |                         |                         | null               | 2021/3/6 오후 6:00:00   | 059d870e689d5cc19c35f5eb398214d7d9856373 | 2021/3/6 오후 6:00:00   | 1473  |              | 633424  | 1431942            | 633424  | 10   | 9              | 0                     | 917               | 215               | 5867                        | 10        | US-DE           | 2021/4/26 오전 12:06:49 | US          | 545070   |                   |                          |           | 13               |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

Azure Blob Storage에서 호스트되는 다양한 데이터 세트 파일 형식의 URL:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet

Pandas의 http URL에서 기본 제공 기능 다운로드를 사용하여 데이터 세트 파일을 다운로드합니다. Pandas에는 다양한 파일 형식에 대한 리더가 있습니다.

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_tracking/latest/covid_tracking.parquet ")
df.head(10)

df.dtypes

df.groupby('state').first().filter(['date','positive', 'death'])

df.groupby(df.state).agg({'state': 'count','positive_increase': 'sum','death_increase': 'sum'})

df_NY=df[df['state'] == 'NY']
df_NY.plot(kind='line',x='date',y="positive",grid=True)
df_NY.plot(kind='line',x='date',y="positive_increase",grid=True)
df_NY.plot(kind='line',x='date',y="death",grid=True)
df_NY.plot(kind='line',x='date',y="death_increase",grid=True)

df_US=df.groupby(df.date).agg({'positive': 'sum','positive_increase': 'sum','death':'sum','death_increase': 'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="positive",grid=True)
df_US.plot(kind='line',x='date',y="positive_increase",grid=True)
df_US.plot(kind='line',x='date',y="death",grid=True)
df_US.plot(kind='line',x='date',y="death_increase",grid=True)



```

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_tracking/latest/covid_tracking.parquet"
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
