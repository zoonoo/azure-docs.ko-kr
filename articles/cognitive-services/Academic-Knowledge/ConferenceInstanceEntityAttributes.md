---
title: Conference Instance 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Conference Instance 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146571"
---
# <a name="conference-instance-entity"></a>Conference Instance 엔터티

> [!NOTE]
> 다음 특성은 회의 인스턴스 엔터티와 관련 됩니다. (Ty = ' 4 ')

name | 설명 | Type | 운영
--- | --- | --- | ---
CC      |컨퍼런스 인스턴스의 총 인용 횟수           |Int32      |없음  
CD.D    |컨퍼런스 인스턴스 이벤트의 날짜    |날짜       |Equals, IsBetween
CD.T    |컨퍼런스 인스턴스 이벤트의 제목   |날짜       |Equals, IsBetween
CIARD   |컨퍼런스 인스턴스의 요약 등록 기한  |날짜       |Equals, IsBetween
CIED    |컨퍼런스 인스턴스의 종료 날짜    |날짜       |Equals, IsBetween
CIFVD   |컨퍼런스 인스턴스의 최종 버전 기한  |날짜       |Equals, IsBetween
CIL     |컨퍼런스 인스턴스의 위치    |string     |Equals, StartsWith
CIN     |회의 인스턴스 정규화 된 이름 |string        |같음
CINDD   |컨퍼런스 인스턴스의 알림 날짜   |날짜       |Equals, IsBetween
CISD    |컨퍼런스 인스턴스의 시작 날짜  |날짜       |Equals, IsBetween
CISDD   |컨퍼런스 인스턴스의 제출 기한     |날짜       |Equals, IsBetween
DCN     |회의 인스턴스 표시 이름  |string      |없음
E | 확장 메타 데이터</br></br>**중요**:이 특성은 사용 되지 않으며 레거시 응용 프로그램에만 지원 됩니다. 이 특성을 개별적으로 요청 (즉, 특성 = Id, Ti, E) 하면 모든 확장 메타 데이터 특성이 *직렬화 된 JSON 문자열* 에서 반환 됩니다.</br></br>이제 확장 된 메타 데이터에 포함 된 모든 특성을 최상위 특성으로 사용할 수 있으며, 이러한 특성 (예: attributes = Id, Ti, DOI, IA)으로 요청할 수 있습니다. | [확장이](#extended) | 없음
ECC     |컨퍼런스 인스턴스의 총 예상 인용 횟수 |Int32      |없음
FN | 컨퍼런스 인스턴스 전체 이름 | string | 없음
Id      |엔터티 ID                              |Int64      |같음
PC | 회의 인스턴스 총 게시 수 | Int32 | 없음
PCS.CN  |인스턴스의 부모 컨퍼런스 계열 이름 |string  |같음
PCS.CId |인스턴스의 부모 컨퍼런스 계열 ID입니다. |Int64     |같음

## <a name="extended"></a>확장이

> [!IMPORTANT]
> 이 특성은 더 이상 사용 되지 않으며 레거시 응용 프로그램에 대해서만 지원 됩니다. 이 특성을 개별적으로 요청 (즉, 특성 = Id, Ti, E) 하면 모든 확장 메타 데이터 특성이 *직렬화 된 JSON 문자열* 에서 반환 됩니다.</br></br>이제 확장 된 메타 데이터에 포함 된 모든 특성을 최상위 특성으로 사용할 수 있으며, 이러한 특성 (예: attributes = Id, Ti, DOI, IA)으로 요청할 수 있습니다.

> [!IMPORTANT]
> "E"를 사용 하 여 개별 확장 특성 요청을 지원 합니다. 범위 (예: "E. DN")는 더 이상 사용 되지 않습니다. 이는 여전히 기술적으로 지원 되지만 "E"를 사용 하 여 개별 확장 특성을 요청 합니다. 범위는 JSON 응답의 두 위치 ("E" 개체의 일부로, 최상위 특성)로 특성 값을 반환 합니다.

name | 설명 | Type | 운영
--- | --- | --- | ---
FN | 컨퍼런스 인스턴스 전체 이름 | string | 없음
PC | 회의 인스턴스 총 게시 수 | Int32 | 없음
