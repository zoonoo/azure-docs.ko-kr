---
title: Field of Study 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API의 Field of Study 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146482"
---
# <a name="field-of-study-entity"></a>Field of Study 엔터티

> [!NOTE]
> 다음 특성은 연구 엔터티의 필드에만 적용 됩니다. (Ty = ' 6 ')

name | 설명 | Type | 운영
--- | --- | --- | ---
CC      |연구 분야의 총 인용 횟수    |Int32      |없음  
DFN     |연구 분야 표시 이름            |string     |없음
ECC     |연구 분야의 총 예상 인용 횟수|Int32      |없음
FL      |연구 분야 계층 구조의 수준     |Int32      |Equals, IsBetween
FN      |연구 분야의 정규화된 이름         |string     |같음
FC.FId  |연구 ID의 자식 필드                |Int64      |같음
FC.FN   |연구 이름의 자식 필드              |string     |같음
FP.FId  |연구 ID의 부모 필드               |Int64      |같음
FP.FN   |연구 이름의 부모 필드             |string     |같음
Id      |엔터티 ID                              |Int64      |같음
PC    | 연구 총 게시 수의 필드 | Int32 | 없음
