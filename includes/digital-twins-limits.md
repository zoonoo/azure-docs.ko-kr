---
author: baanders
description: Azure Digital Twins 제한에 대 한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: fe3c737e0cbf6831e3abc37443e27926ed5e62b8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93091779"
---
### <a name="functional-limits"></a>기능 제한

아래 표에는 Azure Digital Twins의 기능 한도가 나열 되어 있습니다.

| Area | 기능 | 기본 제한 | 빗변? |
| --- | --- | --- | --- |
| Azure 리소스 | 구독 당 지역의 Azure Digital Twins 인스턴스 수 | 10 | 예 |
| Digital Twins | Azure Digital 쌍 인스턴스의 쌍 수 | 200,000 | Yes |
| Digital Twins | 단일 쌍으로 들어오는 관계의 수 | 5,000 | 아니요 |
| Digital Twins | 단일 쌍의 나가는 관계 수 | 5,000 | 아니요 |
| Digital Twins | 단일 쌍의 최대 크기 | 32KB | 아니요 |
| Digital Twins | 최대 요청 페이로드 크기 | 32KB | 아니요 | 
| 라우팅 | 단일 Azure Digital Twins 인스턴스의 끝점 수 | 6 | 아니요 |
| 라우팅 | 단일 Azure Digital Twins 인스턴스의 경로 수 | 6 | 예 |
| 모델 | 단일 Azure Digital Twins 인스턴스 내의 모델 수 | 10000 | Yes |
| 모델 | 단일 API 호출에서 업로드할 수 있는 모델 수 | 250 | 아니요 |
| 모델 | 단일 페이지에 반환 된 항목 수 | 100 | 아니요 |
| 쿼리 | 단일 페이지에 반환 된 항목 수 | 100 | 아니요 |
| 쿼리 | `AND`  /  `OR` 쿼리의 식 수 | 50 | 예 |
| 쿼리 | 절의 배열 항목 수 `IN`  /  `NOT IN` | 50 | 예 |
| 쿼리 | 쿼리의 문자 수 | 8,000 | 예 |
| 쿼리 | 쿼리의 수 `JOINS` | 5 | 예 |

### <a name="rate-limits"></a>속도 제한

다음 표에서는 다양 한 Api의 요율 제한을 반영 합니다.

| API | 기능 | 기본 제한 | 빗변? |
| --- | --- | --- | --- |
| 모델 API | 초당 요청 수 | 100 | 예 |
| Digital Twins API | 초당 요청 수 | 2,000 | Yes |
| Digital Twins API | **모든 쌍 및 관계** 에서 초당 생성/삭제 작업 수 | 50 | 예 |
| Digital Twins API | **단일** 쌍 또는 해당 관계에서 초당 생성/업데이트/삭제 작업 수입니다. | 10 | 예 |
| 쿼리 API | 초당 요청 수 | 500 | Yes |
| 쿼리 API | 초당 [쿼리 단위](../articles/digital-twins/concepts-query-units.md) | 4,000 | Yes |
| 이벤트 경로 API | 초당 요청 수 | 100 | 예 |

### <a name="other-limits"></a>기타 제한

Azure Digital Twins 모델의 DTDL 문서 내에 있는 데이터 형식 및 필드에 대 한 제한은 GitHub: [*DTDL (디지털 Twins 정의 언어)-버전 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)의 사양 설명서 내에서 찾을 수 있습니다.
 
쿼리 대기 시간 정보 및 기타 쿼리 제한은 [*방법: 쌍 그래프 쿼리*](../articles/digital-twins/how-to-query-graph.md)에서 찾을 수 있습니다.