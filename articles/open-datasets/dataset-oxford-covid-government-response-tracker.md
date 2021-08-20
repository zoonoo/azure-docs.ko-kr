---
title: Oxford COVID-19 Government Response Tracker
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 Oxford COVID-19 Government Response Tracker 데이터 세트를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a39c8915027cddeb168f5bb0c63f915492ece398
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444617"
---
# <a name="oxford-covid-19-government-response-tracker"></a>Oxford COVID-19 Government Response Tracker

[OxCGRT(Oxford Covid-19 Government Response Tracker) 데이터 세트](https://github.com/OxCGRT/covid-policy-tracker/)는 어떤 정부가 어떤 조치를 언제 취했는지에 관한 체계적 정보를 포함합니다.

이 정보는 의사 결정자와 시민이 일관된 방식으로 정부 대응을 이해하여 이 세계적인 유행병을 극복하기 위한 노력을 지원하는 데 도움이 될 수 있습니다. OxCGRT는 정부에서 취한 다양한 일반적인 정책 대응에 대한 정보를 체계적으로 수집하고, 기준에 따라 해당 정책을 기록하여 정부 조치의 범위를 반영하고, 이 점수를 정책 인덱스 모음으로 집계합니다.


[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>데이터 세트

수정된 버전의 데이터 세트는 CSV, JSON, JSON-Lines 및 Parquet 형식으로 사용 가능하며, 매일 업데이트됩니다.
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

모든 수정된 버전은 iso_country 코드와 추가된 로드 시간이 있고, 밑줄 구분 기호가 있는 소문자 형식의 열 이름을 사용합니다.

원시 데이터: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/latest/CovidPolicyTracker.csv

수정된 원시 데이터의 이전 버전: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/

## <a name="data-volume"></a>데이터 볼륨

2020년 6월 8일 기준, 27,919개의 행을 포함합니다(CSV 4.9MB, JSON 20.9MB, JSONL 20.8MB, Parquet 133.0KB).

## <a name="data-source"></a>데이터 원본

이 데이터의 출처는 Thomas Hale, Sam Webster, Anna Petherick, Toby Phillips 및 Beatriz Kira입니다. (2020년). Oxford COVID-19 Government Response Tracker. [Blavatnik School of Government](https://www.bsg.ox.ac.uk/). 원시 데이터는 [최신 OxCGRT csv 파일](https://github.com/OxCGRT/covid-policy-tracker/blob/master/data/OxCGRT_latest.csv)에서 매일 수집됩니다. 수집 방법을 포함하여 이 데이터 세트에 관한 자세한 내용은 [Government Response Tracker 사이트](https://www.bsg.ox.ac.uk/research/research-projects/covid-19-government-response-tracker)를 참조하세요.

## <a name="data-quality"></a>데이터 품질
OxCGRT는 데이터의 정확성 또는 적시성을 보장하지 않습니다. 자세한 내용은 [데이터 품질 설명](https://github.com/OxCGRT/covid-policy-tracker#data-quality)을 참조하세요.

## <a name="license-and-use-rights-attribution"></a>라이선스 및 사용 권한 표시

이 데이터는 [Creative Commons Attribution 4.0 International License](https://github.com/OxCGRT/covid-policy-tracker/blob/master/LICENSE.txt)에 따라 라이선스가 부여됩니다.

다음과 같이 인용합니다. Thomas Hale, Sam Webster, Anna Petherick, Toby Phillips 및 Beatriz Kira. (2020년). Oxford COVID-19 Government Response Tracker. Blavatnik School of Government.

## <a name="contact"></a>연락처

코로나19 Data Lake의 이 데이터 세트나 기타 데이터 세트에 대한 질문이나 피드백은 askcovid19dl@microsoft.com에 문의하세요.

## <a name="columns"></a>열

| 이름                                  | 데이터 형식 | 고유한 | 값(샘플)            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------|-----------|--------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| c1_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그입니다. 0 - 대상 1 - 일반 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c1_school_closing                     | double    | 5      | 3.0 2.0                    | 학교 및 대학교 폐쇄를 기록합니다. 0 - 측정값 없음 1 - 폐쇄가 권장됨 2 - 폐쇄 필요(일부 수준 또는 범주만 해당, 예: 고등학교만 또는 공립학교만) 3 - 모든 수준 폐쇄 필요 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                 |
| c2_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그입니다. 0 - 대상, 1 - 일반, 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| c2_workplace_closing                  | double    | 5      | 2.0 1.0                    | 직장 폐쇄를 기록합니다. 0 - 측정값 없음 1 - 폐쇄가 권장됨(또는 재택근무가 권장됨) 2 - 일부 부문 또는 범주의 근로자에 대한 폐쇄 필요(또는 재택근무) 3 - 필수 부문(예: 식료품점, 의사)을 제외한 모든 부문의 직장 폐쇄 필요(또는 재택근무) 비어 있음 - 데이터 없음                                                                                                                                                                                                                          |
| c3_cancel_public_events               | double    | 4      | 2.0 1.0                    | 퍼블릭 이벤트 취소를 기록합니다. 0 - 측정값 없음 1 - 취소 권장 2 - 취소 필요 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                    |
| c3_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그입니다. 0 - 대상 1 - 일반 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c4_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그 0 - 대상 1 - 일반 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c4_restrictions_on_gatherings         | double    | 6      | 4.0 3.0                    | 사적인 모임에 대한 제한을 기록합니다. 0 - 제한 없음 1 - 매우 규모가 큰 모임에 대한 제한(제한이 1,000명 이상임) 2 - 101~1,000명의 모임에 대한 제한 3 - 11~100명의 모임에 대한 제한 4 - 10명 이하의 모임에 대한 제한 비어 있음 - 데이터 없음                                                                                                                                                                                                                 |
| c5_close_public_transport             | double    | 4      | 1.0 2.0                    | 대중교통 폐쇄를 기록합니다. 0 - 측정값 없음 1 - 폐쇄가 권장됨(또는 이용 가능한 교통의 양/경로/수단을 크게 줄임) 2 - 폐쇄 필요(또는 대부분 시민의 이용 금지) 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                          |
| c5_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그 0 - 대상 1 - 일반 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그 0 - 대상 1 - 일반 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_stay_at_home_requirements          | double    | 5      | 1.0 2.0                    | “대피소”(또는 집에만 머물러야 함)에 대한 명령을 기록합니다. 0 - 측정값 없음 1 - 집에 머무르기를 권장함 2 - 집에 머물러야 함(단, 매일 운동, 식료품 쇼핑 및 ‘필수적인’ 이동은 예외적으로 허용됨) 3 - 집에 머물러야 하고 최소한의 예외(예: 일주일에 한 번 집 밖에 나가는 것 허용 또는 한 번에 한 명만 집 밖에 나갈 수 있음 등)만 허용됨 비어 있음 - 데이터 없음                                                                                                                                                |
| c7_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그 0 - 대상 1 - 일반 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c7_restrictions_on_internal_movement  | double    | 4      | 2.0 1.0                    | 도시/지역 간 내부 이동에 대한 제한을 기록합니다. 0 - 측정값 없음 1 - 지역/도시 간에 이동하지 않도록 권장함 2 - 내부 이동 제한이 적용됨 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                      |
| c8_international_travel_controls      | double    | 6      | 3.0 4.0                    | 해외여행에 대한 제한을 기록합니다. 참고: 시민이 아니라 해외 여행객에 대한 정책을 기록합니다. 0 - 제한 없음 1 - 도착한 여행객 심사 2 - 일부 또는 모든 지역의 도착한 여행객 격리 3 - 일부 지역으로부터의 도착 금지 4 - 모든 지역에 대한 금지 또는 완전한 국경 폐쇄 비어 있음 - 데이터 없음                                                                                                                                                                                                                           |
| confirmedcases                        | smallint  | 18,238 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| confirmeddeaths                       | smallint  | 14,906 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countrycode                           | 문자열    | 186    | USA BRA                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countryname                           | 문자열    | 186    | United States Brazil       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| date                                  | date      | 478    | 2020-08-25 2021-03-30      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e1_flag                               | boolean   | 3      | True                       | 부문 범위의 이진 플래그 0 - 공식 근로자만 해당 1 - 비공식 근로자로 이전함 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                          |
| e1_income_support                     | double    | 4      | 1.0 2.0                    | 정부가 실직하거나 일할 수 없는 사람에게 직접 현금 지급을 제공하는지를 기록합니다. 참고: 명시적으로 급여/월급과 관련된 경우 회사에 대한 지급만 포함합니다. 0 - 소득 지원 없음 1 - 정부가 손실 월급의 50% 미만을 대신 제공함(또는 균일한 금액인 경우 중앙값 월급의 50%보다 작음) 2 - 정부가 손실 월급의 50% 이상을 대신 제공함(또는 균일한 금액인 경우 중앙값 월급의 50%보다 큼) 비어 있음 - 데이터 없음                                                                        |
| e2_debt/contract_relief               | double    | 4      | 1.0 2.0                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e3_fiscal_measures                    | double    | 819    | -0.01 3.0                  | 경기 부양 지출이 발표되었습니다. 참고: 재정 부양의 이전에 발표된 지출 기록 금액(USD)에 추가적인 금액만 기록합니다(E4, H4 또는 H5에 포함되지 않은 모든 지출 또는 세금 감면 포함). 0 - 해당 일에 새로운 지출 없음 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                               |
| e4_international_support              | double    | 113    | -0.02 5000000.0            | 다른 국가에 대한 코로나19 관련 원조 지출 제공이 발표되었습니다. 참고: 이전에 발표된 지출 기록 금액(USD)에 추가적인 금액만 기록합니다. 0 - 해당 일에 새로운 지출 없음 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                 |
| h1_flag                               | boolean   | 3      | True                       | 지리적 범위의 이진 플래그 0 - 대상 1 - 일반 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| h1_public_information_campaigns       | double    | 4      | 2.0 1.0                    | 대중 정보 캠페인의 존재를 기록합니다. 0 - 코로나19 대중 정보 캠페인이 없음 1 - 공무원이 코로나19에 대한 주의를 촉구함 2 - 조직화된 대중 정보 캠페인(예: 기존 미디어와 소셜 미디어 이용) 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                         |
| h2_testing_policy                     | double    | 5      | 2.0 1.0                    | 검사를 이용할 수 있는 사람에 대한 정부 정책을 기록합니다. 참고: 면역력 검사(항체 검사)가 아니라 현재 감염 검사(PCR 검사)에 대한 정책을 기록합니다. 0 - 검사 정책 없음 1 - (a) 증상이 있고 (b) 특정 조건(예: 핵심 근로자, 병원에 입원함, 알려진 사례와 접촉했음, 해외에서 돌아옴)을 충족하는 사람만 검사함 2 - 코로나19 증상을 보이는 모든 사람을 검사함 3 - 개방형 대중 검사(예: 증상이 없는 사람이 “드라이브스루” 검사를 이용할 수 있음) 비어 있음 - 데이터 없음 |
| h3_contact_tracing                    | double    | 4      | 2.0 1.0                    | 양성 진단 후 접촉 추적에 대한 정부 정책을 기록합니다. 참고: 코로나19에 잠재적으로 노출된 모든 사람을 식별하는 정책을 찾고 있습니다. 자발적 Bluetooth 앱으로는 이 기능을 얻을 수 없습니다. 0 - 접촉 추적 없음 1 - 제한된 접촉 추적(일부 경우에 수행됨) 2 - 포괄적인 접촉 추적(식별된 모든 경우에 수행됨)                                                                                                                                                          |
| h4_emergency_investment_in_healthcare | double    | 462    | 35.0 562.0                 | 의료 서비스 시스템(예: 병원, 마스크 등)에 대한 단기 지출이 발표되었습니다. 참고: 이전에 발표된 지출 기록 금액(USD)에 추가적인 금액만 기록합니다. 0 - 해당 일에 새로운 지출 없음 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                         |
| h5_investment_in_vaccines             | double    | 133    | 1.0 191.0                  | 코로나19 백신 개발에 대한 공공 지출이 발표되었습니다. 참고: 이전에 발표된 지출 기록 금액(USD)에 추가적인 금액만 기록합니다. 0 - 해당 일에 새로운 지출 없음 비어 있음 - 데이터 없음                                                                                                                                                                                                                                                                                                                            |
| iso_country                           | 문자열    | 186    | US BR                      | ISO 3166 국가 또는 지역 코드                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| load_date                             | timestamp | 1      | 2021-04-26 00:06:25.157000 | 외부 원본에서 데이터가 로드된 날짜 및 시간                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| stringencyindex                       | double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| stringencyindexfordisplay             | double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="preview"></a>미리 보기

| countryname | countrycode | date       | c1_school_closing | c2_workplace_closing | c3_cancel_public_events | c4_restrictions_on_gatherings | c5_close_public_transport | c6_stay_at_home_requirements | c7_restrictions_on_internal_movement | c8_international_travel_controls | e1_income_support | e2_debt/contract_relief | e3_fiscal_measures | e4_international_support | h1_public_information_campaigns | h2_testing_policy | h3_contact_tracing | h4_emergency_investment_in_healthcare | h5_investment_in_vaccines | m1_wildcard | stringencyindex | stringencyindexfordisplay | iso_country | load_date             |
|-------------|-------------|------------|-------------------|----------------------|-------------------------|-------------------------------|---------------------------|------------------------------|--------------------------------------|----------------------------------|-------------------|-------------------------|--------------------|--------------------------|---------------------------------|-------------------|--------------------|---------------------------------------|---------------------------|-------------|-----------------|---------------------------|-------------|-----------------------|
| 아루바       | ABW         | 2020-01-01 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-02 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-03 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-04 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-05 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-06 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-07 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-08 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-09 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| 아루바       | ABW         | 2020-01-10 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |

## <a name="data-access"></a>데이터 액세스

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker)** .

## <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-oxford-covid-19-government-response-tracker-oxcgrt-dataset"></a>이 Notebook은 OxCGRT(Oxford Covid-19 Government Response Tracker) 데이터 세트에 액세스하기 위한 URL 및 샘플 코드를 문서화합니다.

Azure Blob Storage에서 호스트되는 다양한 파일 형식의 URL:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv 

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl 

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

Pandas의 기본 제공 기능인 http URL에서 다운로드를 사용하여 데이터 세트 파일을 다운로드합니다. Pandas에는 다양한 파일 형식을 위한 읽기 프로그램이 있습니다.

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


먼저 pandas 데이터 프레임에 데이터 세트 파일을 로드하고 몇 가지 샘플 행을 봅니다.

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet")
df.head(10)
```

다양한 필드의 데이터 형식을 확인하고 업데이트된 열이 datetime 형식인지 확인하겠습니다.

```python
df.dtypes
```

이 데이터 세트는 다양한 국가에 대한 데이터를 포함합니다. 데이터를 보유한 국가를 확인하겠습니다.

먼저 각 국가의 최신 데이터를 살펴봅니다.

```python
df.groupby('countryname').first().filter(['confirmedcases ', 'confirmeddeaths','h5_investment_in_vaccines',
    'c6_stay_at_home_requirements','h4_emergency_investment_in_healthcare','c4_restrictions_on_gatherings', 'load_date'])
```

다음으로, `confirmedcases` 및 `confirmeddeaths`와 같은 열이 최신 데이터로 집계되도록 몇 가지 집계를 수행합니다. 위의 표에 나와 있는 가장 늦은 날짜의 양성과 사망 수가 `confirmedcases` 및 `confirmeddeaths`의 집계와 일치하는지 확인해야 합니다.


```python
df.groupby('countryname').agg({'countryname': 'count','confirmedcases': 'sum','confirmeddeaths': 'sum',
                               'h5_investment_in_vaccines': 'count', 'c6_stay_at_home_requirements':'sum'})
```

몇몇 국가에 대한 일부 기본 시각화를 수행하겠습니다.

```python
import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countryname', 'confirmedcases', 
'confirmeddeaths']].groupby(['countryname']).max().sort_values(by='confirmedcases', 
                                           ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')
```

```python
df_US = df.groupby(df.date).agg({'confirmedcases': 'sum','confirmeddeaths':'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="confirmedcases",grid=True)
df_US.plot(kind='line',x='date',y="confirmeddeaths",grid=True)

```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

이 플랫폼/패키지 조합에는 샘플을 사용할 수 없습니다.

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
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

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[대신 Notebook을 다운로드합니다](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** .

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
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
