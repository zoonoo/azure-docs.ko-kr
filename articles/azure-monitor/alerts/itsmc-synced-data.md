---
title: ITSM 제품에서 LA 작업 영역으로 동기화 된 데이터
description: 이 문서에서는 ITSM 제품에서 LA 작업 영역으로 동기화 된 데이터에 대 한 개요를 제공 합니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 83994c1b6e150342a777a079d79d6a594d30c3ff
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041606"
---
# <a name="data-synced-from-your-itsm-product"></a>ITSM 제품에서 동기화 된 데이터

인시던트 및 변경 요청은 연결의 구성 ("동기화 데이터" 필드 사용)에 따라 ITSM 도구에서 Log Analytics 작업 영역으로 동기화 됩니다.
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>동기화 된 데이터

이 섹션에서는 ITSMC에서 수집한 데이터의 몇 가지 예를 보여 줍니다.

**ServiceDesk_CL** 필드는 Log Analytics으로 가져오는 작업 항목 형식에 따라 달라 집니다. 다음은 두 개의 작업 항목 형식에 대 한 필드 목록입니다.

**작업 항목:** **인시던트**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- 서비스 데스크 ID
- 시스템 상태
- 긴급도
- 영향
- 우선 순위
- 에스컬레이션
- 만든 사람
- 해결한 사람
- 종결한 사람
- 원본
- 할당 대상
- Category
- 제목
- Description
- 만든 날짜
- 종결한 날짜
- 해결한 날짜
- 마지막으로 수정한 날짜
- Computer

**작업 항목:** **변경 요청**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- 서비스 데스크 ID
- 만든 사람
- 종결한 사람
- 원본
- 할당 대상
- 제목
- Type
- Category
- 시스템 상태
- 에스컬레이션
- 충돌 상태
- 긴급도
- 우선 순위
- 위험
- 영향
- 할당 대상
- 만든 날짜
- 종결한 날짜
- 마지막으로 수정한 날짜
- 요청한 날짜
- 예상된 시작 날짜
- 예상된 종료 날짜
- 작업 시작 날짜
- 작업 종료 날짜
- Description
- Computer

## <a name="servicenow-example"></a>ServiceNow 예 
### <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 인시던트에 대한 출력 데이터

| Log Analytics 필드 | ServiceNow 필드 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | 시스템 상태 |
| Urgency_s |긴급도 |
| Impact_s |영향|
| Priority_s | 우선 순위 |
| CreatedBy_s | 보고자 |
| ResolvedBy_s | 해결한 사람|
| ClosedBy_s  | 종결한 사람 |
| Source_s| 연락처 유형 |
| AssignedTo_s | 할당 대상  |
| Category_s | Category |
| Title_s|  간단한 설명 |
| Description_s|  메모 |
| CreatedDate_t|  열림 |
| ClosedDate_t| closed|
| ResolvedDate_t|해결됨|
| Computer  | 구성 항목 |

### <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 변경 요청에 대한 출력 데이터

| Log Analytics | ServiceNow 필드 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 요청자 |
| ClosedBy_s | 종결한 사람 |
| AssignedTo_s | 할당 대상  |
| Title_s|  간단한 설명 |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  시스템 상태|
| Urgency_s|  긴급도 |
| Priority_s| 우선 순위|
| Risk_s| 위험|
| Impact_s| 영향|
| RequestedDate_t  | 요청한 날짜 |
| ClosedDate_t | 종결한 날짜 |
| PlannedStartDate_t  | 예상된 시작 날짜 |
| PlannedEndDate_t  | 예상된 종료 날짜 |
| WorkStartDate_t  | 실제 시작 날짜 |
| WorkEndDate_t | 실제 종료 날짜|
| Description_s | Description |
| Computer  | 구성 항목 |

## <a name="next-steps"></a>다음 단계

* [ITSM 커넥터의 문제 해결](./itsmc-resync-servicenow.md)
