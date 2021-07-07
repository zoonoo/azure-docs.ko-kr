---
title: 코로나19 Data Lake
titleSuffix: Azure Open Datasets
description: Azure Open Datasets에서 코로나19 Data Lake를 사용하는 방법을 알아봅니다.
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: e43ea946511efaa313204ca26bf7e05f1b3f923a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112039112"
---
# <a name="covid-19-data-lake"></a>코로나19 Data Lake

코로나19 Data Lake에는 다양한 출처의 코로나19 관련 데이터 세트가 포함되어 있습니다. 여기에는 테스트 및 환자 결과 추적 데이터, 사회적 거리두기 정책, 병원 수용 능력, 이동성 등이 포함됩니다.

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

코로나19 데이터 레이크는 미국 동부 지역의 Azure Data Lake Storage에서 호스트됩니다. 각 데이터 세트에 대해 csv, json, json-lines, parquet 형식으로 수정된 버전도 사용할 수 있습니다. 원시 데이터도 수집된 형태로 사용할 수 있습니다.

조인을 간소화하기 위해 원래 포함되지 않은 ISO 3166 하위 코드가 추가됩니다. 열 이름의 서식은 밑줄 구분 기호가 포함된 소문자로 다시 지정되었습니다. 데이터 세트는 매일 업데이트되며 수정된 원시 파일의 기록 복사본도 사용 가능합니다.

## <a name="datasets"></a>데이터 세트

| 데이터 세트                                                                 | Description                                                                                                                                                                                                                                             |
|--------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Bing 코로나19 데이터](dataset-bing-covid-19.md)                                                       | Bing 코로나19 데이터에는 모든 지역의 확진, 사망 및 완치 사례가 포함되며, 이 데이터는 매일 업데이트됩니다.                                                                                                                                                      |
| [코로나 추적 프로젝트](dataset-covid-tracking.md)                                                | COVID Tracking Project 데이터 세트는 미국 전 지역 및 영토의 최신 검사 수, 확인된 사례, 입원, 환자 결과를 제공합니다.                                                                                     |
| [ECDC(European Center for Disease Prevention and Control) 코로나19 사례](dataset-ecdc-covid-cases.md) | ECDC(European Center for Disease Prevention and Control)에서 제공하는 전 세계 코로나19의 지역 전파에 대한 사용 가능한 최신 공개 데이터입니다. 각 행/항목에는 일별 및 국가 또는 지역별로 보고되는 신규 사례 건수가 포함되어 있습니다. |
| [Oxford COVID-19 Government Response Tracker](dataset-oxford-covid-government-response-tracker.md)                              | OxCGRT(Oxford Covid-19 Government Response Tracker) 데이터 세트는 어떤 정부가 어떤 조치를 언제 취했는지에 대한 체계적 정보를 포함합니다.                                                                                              |

---

## <a name="next-steps"></a>다음 단계

[Open Datasets 카탈로그](dataset-catalog.md)에서 나머지 데이터 세트를 봅니다.