---
title: Conference Series 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Conference Series 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: ecd8042212987849d9d302642f03e59493235599
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143917"
---
# <a name="conference-series-entity"></a>Conference Series 엔터티

> [!NOTE]
> 다음 특성은 회의 시리즈 엔터티와 관련 됩니다. (Ty = ' 3 ')

name | 설명 | Type | 운영
--- | --- | --- | ---
CC      |컨퍼런스 시리즈의 총 인용 횟수         |Int32      |없음  
CN      |컨퍼런스 시리즈의 정규화된 이름      |string     |같음
DCN     |컨퍼런스 시리즈 표시 이름         |string     |없음
ECC     |컨퍼런스 시리즈의 총 예상 인용 횟수   |Int32      |없음
F.FId   |컨퍼런스 시리즈와 연결된 연구 분야 엔터티 ID |Int64  | 같음
F.FN    |컨퍼런스 시리즈와 연결된 연구 분야 이름  | Equals,<br/>StartsWith
Id      |엔터티 ID                              |Int64      |같음
PC    |컨퍼런스 시리즈 총 게시 수 |Int32 | 없음
