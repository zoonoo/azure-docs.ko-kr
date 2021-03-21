---
title: 모든 데이터 집합 API 가져오기
description: 이 API를 사용 하 여 상업적 marketplace 분석에 사용할 수 있는 모든 데이터 집합을 가져옵니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583946"
---
# <a name="get-all-datasets-api"></a>모든 데이터 집합 API 가져오기

모든 데이터 집합 가져오기 API는 사용 가능한 데이터 집합을 모두 가져옵니다. 데이터 집합 테이블, 열, 메트릭 및 시간 범위를 나열 합니다.

**요청 구문**

| **방법** | **요청 URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**요청 헤더**

| **Header** | **Type** | **설명** |
| --- | --- | --- |
| 권한 부여 | 문자열 | 필수 요소. 양식의 Azure AD (Azure Active Directory) 액세스 토큰 `Bearer <token>` |
| 콘텐츠 형식 | 문자열 | `Application/JSON` |

**Path 매개 변수**

없음

**쿼리 매개 변수**

| **매개 변수 이름** | **유형** | **필수** | **설명** |
| --- | --- | --- | --- |
| `datasetName` | 문자열 | No | 하나의 데이터 집합에 대 한 세부 정보만 가져오도록 필터링 |

**요청 페이로드**

없음

**용어 설명**

없음

**응답**

응답 페이로드는 다음과 같이 구성 됩니다.

응답 코드: 200, 400, 401, 403, 404, 500

응답 페이로드 예:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**용어 설명**

다음 표에서는 응답의 주요 요소를 정의 합니다.

| **매개 변수** | **설명** |
| --- | --- |
| `DatasetName` | 이 배열 개체가 정의 하는 데이터 집합의 이름입니다. |
| `SelectableColumns` | Select 열에 지정할 수 있는 원시 열 |
| `AvailableMetrics` | Select 열에 지정할 수 있는 집계/메트릭 열 이름 |
| `AvailableDateRanges` | 데이터 집합에 대 한 보고서 쿼리에서 사용할 수 있는 날짜 범위 |
| `NextLink` | 데이터 페이지가 매겨진 경우 다음 페이지로 연결 |
| `TotalCount` | 값 배열의 데이터 집합 수 |
| `StatusCode` | 결과 코드입니다. 가능한 값은 200, 400, 401, 403, 500입니다. |
