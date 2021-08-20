---
title: ECDC(European Center for Disease Prevention and Control) 코로나19 사례
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 ECDC(European Center for Disease Prevention and Control) 코로나19 사례 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 31868ee27e531e70df4c89944f0baf888527a190
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447603"
---
# <a name="european-centre-for-disease-prevention-and-control-ecdc-covid-19-cases"></a>ECDC(European Center for Disease Prevention and Control) 코로나19 사례

ECDC(European Center for Disease Prevention and Control)에서 제공하는 전 세계 코로나19의 지역 전파에 대한 [사용 가능한 최신 공개 데이터](https://www.ecdc.europa.eu/en/publications-data/download-todays-data-geographic-distribution-covid-19-cases-worldwide)입니다. 각 행/항목에는 일별 및 국가 또는 지역별로 보고되는 신규 사례 건수가 포함되어 있습니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>데이터 세트

수정된 버전의 데이터 세트는 CSV, JSON, JSON-Lines 및 Parquet 형식으로 사용 가능하며, 매일 업데이트됩니다.
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

모든 수정된 버전은 iso_country_region 코드와 추가된 로드 시간이 있고, 밑줄 구분 기호가 있는 소문자 형식의 열 이름을 사용합니다.

원시 데이터: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/latest/ECDCCases.csv

수정된 원시 데이터의 이전 버전: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/ecdc_cases/

## <a name="data-volume"></a>데이터 볼륨
2020년 5월 28일 기준, 19,876개의 행을 포함합니다(CSV 1.5MB, JSON 4.9MB, JSONL 4.9MB, Parquet 54.1KB).

## <a name="data-source"></a>데이터 원본

원시 데이터는 [ECDC csv 파일](https://opendata.ecdc.europa.eu/covid19/casedistribution/csv)에서 매일 수집됩니다. 원본을 포함하여 이 데이터 세트에 대한 자세한 내용은 [ECDC 데이터 수집 페이지](https://www.ecdc.europa.eu/en/covid-19/data-collection)를 참조하세요.

## <a name="data-quality"></a>데이터 품질
ECDC는 데이터의 정확성 또는 적시성을 보장하지 않습니다. [부인을 읽어보세요](https://www.ecdc.europa.eu/en/covid-19/data-collection).

## <a name="license-and-use-rights-attribution"></a>라이선스 및 사용 권한 표시

이 데이터는 ECDC 저작권 정책(여기)에서 허가한 대로 제공되며 사용할 수 있습니다. 저작권이 타사에 있는 모든 문서의 경우 복제에 대한 권한은 저작권 소유자에게 받아야 합니다.

ECDC는 항상 이 데이터의 원래 원본으로 인정되어야 합니다. 자료의 각 복사본에 해당 인정 내용이 포함되어야 합니다.

## <a name="contact"></a>연락처

이 데이터 세트나 코로나19 데이터 레이크의 다른 데이터 세트에 대한 질문이나 피드백이 있는 경우 askcovid19dl@microsoft.com으로 문의하세요.

## <a name="columns"></a>열

| 이름                      | 데이터 형식 | 고유한 | 값(샘플)            | Description                            |
|---------------------------|-----------|--------|----------------------------|----------------------------------------|
| cases                     | smallint  | 5,515  | 1 2                        | 보고된 사례 수               |
| continent_exp             | 문자열    | 6      | 유럽 아프리카              | 대륙 이름                         |
| countries_and_territories | 문자열    | 214    | 캐나다 벨기에             | 국가 또는 지역 이름              |
| country_territory_code    | 문자열    | 213    | KOR ISL                    | 3자로 된 국가 또는 지역 코드 |
| date_rep                  | date      | 350    | 2020-12-11 2020-11-22      | 보고서 날짜                     |
| 일                       | smallint  | 31     | 14 13                      | 달중 일수                           |
| deaths                    | smallint  | 1,049  | 1 2                        | 보고된 사망 수              |
| geo_id                    | 문자열    | 214    | CA SE                      | 지역 식별자                         |
| iso_country               | 문자열    | 214    | SE US                      | ISO 3166 국가 또는 지역 코드        |
| load_date                 | timestamp | 1      | 2021-04-26 00:06:22.123000 | 데이터가 Azure로 로드된 날짜      |
| month                     | smallint  | 12     | 10 8                       | 월 번호                           |
| 연도                      | smallint  | 2      | 2020 2019                  | Year                                   |

## <a name="preview"></a>미리 보기

| date_rep   | 일 | month | 연도 | cases | deaths | countries_and_territories | geo_id | country_territory_code | continent_exp | load_date             | iso_country |
|------------|-----|-------|------|-------|--------|---------------------------|--------|------------------------|---------------|-----------------------|-------------|
| 2020-12-14 | 14  | 12    | 2020 | 746   | 6      | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-13 | 13  | 12    | 2020 | 298   | 9      | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-12 | 12  | 12    | 2020 | 113   | 11     | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-11 | 11  | 12    | 2020 | 63    | 10     | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-10 | 10  | 12    | 2020 | 202   | 16     | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-09 | 9   | 12    | 2020 | 135   | 13     | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-08 | 8   | 12    | 2020 | 200   | 6      | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-07 | 7   | 12    | 2020 | 210   | 26     | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-06 | 6   | 12    | 2020 | 234   | 10     | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |
| 2020-12-05 | 5   | 12    | 2020 | 235   | 18     | 아프가니스탄               | AF     | AFG                    | 아시아          | 4/26/2021 12:06:22 AM | AF          |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 Notebook에는 ECDC(European Center for Disease Prevention and Control) 코로나19 사례 데이터 세트에 액세스하기 위한 URL과 샘플 코드가 포함되어 있습니다. Azure Blob Storage에서 호스트된 다양한 데이터 세트 파일 형식의 URL:¶ CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet

Pandas의 기본 제공 기능인 http URL에서 다운로드를 사용하여 데이터 세트 파일을 다운로드합니다. Pandas에는 다양한 파일 형식을 위한 읽기 프로그램이 있습니다.

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet")
df.head(10)

df.dtypes

df.groupby('countries_and_territories').first().filter(['continent_exp','cases', 'deaths','date_rep'])

df.groupby('continent_exp').agg({'countries_and_territories': 'count','cases': 'count','deaths': 'count'})

import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countries_and_territories', 'cases', 'deaths']].groupby(['countries_and_territories'
         ]).max().sort_values(by='cases',ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')

df_Worldwide=df[df['countries_and_territories']=='United_States_of_America']

df.plot(kind='line',x='date_rep',y="cases",grid=True)
df.plot(kind='line',x='date_rep',y="deaths",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="confirmed_change",grid=True)
#df_Worldwide.plot(kind='line',x='date_rep',y="deaths_change",grid=True)

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
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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
blob_relative_path = "curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet"
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

## <a name="examples"></a>예

이 데이터 세트를 사용하는 방법의 예를 참조하세요.

- [Synapse SQL 서버리스 엔드포인트를 사용하여 코로나 데이터 분석](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/how-azure-synapse-analytics-helps-you-analyze-covid-data-with/ba-p/1457836)
- [Azure Synapse Analytics의 SQL 엔드포인트를 사용하는, 코로나 데이터에 대한 선형 회귀 분석](https://techcommunity.microsoft.com/t5/azure-synapse-analytics/linear-regression-analysis-on-covid-data-using-sql-endpoint-in/ba-p/1468697)

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.
