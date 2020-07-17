---
author: baanders
description: Azure Digital Twins 제한에 대 한 포함 파일
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 76ccd6f42f2cfd83512a2e817e8c2ac9aa61a28b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391492"
---
### <a name="functional-limits"></a>기능 제한

아래 표에는 현재 미리 보기에서 Azure Digital Twins의 기능 제한이 나열 되어 있습니다.

| Area | 기능 | 기본 제한 | 빗변? |
| --- | --- | --- | --- |
| Azure 리소스 | 구독 당 지역의 Azure Digital Twins 인스턴스 수 | 10 | Yes |
| 디지털 쌍 | Azure Digital 쌍 인스턴스의 쌍 수 | 200,000 | Yes |
| 라우팅 | 단일 Azure Digital Twins 인스턴스의 끝점 수 | 6 | No |
| 라우팅 | 단일 Azure Digital Twins 인스턴스의 경로 수 | 6 | Yes |
| 모델 | 단일 Azure Digital Twins 인스턴스 내의 모델 수 | 10000 | Yes |
| 모델 | 단일 API 호출에서 업로드할 수 있는 모델 수 | 250 | No |
| 모델 | 단일 페이지에 반환 된 항목 수 | 100 | No |
| 쿼리 | 단일 페이지에 반환 된 항목 수 | 100 | No |
| 쿼리 | `AND`  /  `OR` 쿼리의 식 수 | 50 | 예 |
| 쿼리 | 절의 배열 항목 수 `IN`  /  `NOT IN` | 50 | 예 |
| 쿼리 | 쿼리의 문자 수 | 8,000 | 예 |
| 쿼리 | 쿼리의 수 `JOINS` | 1 | Yes |

### <a name="rate-limits"></a>속도 제한

이 표에는 다른 Api의 요율 제한이 반영 되어 있습니다.

| API | 기능 | 기본 제한 | 빗변? |
| --- | --- | --- | --- |
| 모델 API | 초당 요청 수 | 100 | 예 |
| Digital Twins API | 초당 요청 수 | 1,000 | Yes |
| 쿼리 API | 초당 요청 수 | 500 | Yes |
| 쿼리 API | 초당 쿼리 단위 | 4,000 | Yes |
| 이벤트 경로 API | 초당 요청 수 | 100 | 예 |

### <a name="other-limits"></a>기타 제한

Azure Digital Twins 모델의 DTDL 문서 내에 있는 데이터 형식 및 필드에 대 한 제한은 GitHub: [DTDL (디지털 Twins 정의 언어)-버전 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)의 사양 설명서 내에서 찾을 수 있습니다.
 
쿼리 대기 시간 정보 및 미리 보기 중 쿼리 작성에 대 한 기타 지침은 [방법: 쌍 그래프 쿼리](../articles/digital-twins/how-to-query-graph.md)를 참조 하세요.