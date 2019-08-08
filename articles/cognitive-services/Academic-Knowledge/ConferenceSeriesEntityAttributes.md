---
title: Conference Series 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Conference Series 엔터티와 함께 사용할 수 있는 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705037"
---
# <a name="conference-series-entity"></a>Conference Series 엔터티

<sub> *다음 특성은 Conference Series 엔터티에 한정됩니다. (Ty = '3') </sub>

이름    |Description                            |형식       | 작업
------- | ------------------------------------- | --------- | ----------------------------
ID      |엔터티 ID                              |Int64      |같음
CN      |컨퍼런스 시리즈의 정규화된 이름      |문자열     |같음
DCN     |컨퍼런스 시리즈 표시 이름         |String     |없음
CC      |컨퍼런스 시리즈의 총 인용 횟수         |Int32      |없음  
ECC     |컨퍼런스 시리즈의 총 예상 인용 횟수   |Int32      |없음
F.FId   |컨퍼런스 시리즈와 연결된 연구 분야 엔터티 ID |Int64  | 같음
F.FN    |컨퍼런스 시리즈와 연결된 연구 분야 이름  | Equals,<br/>StartsWith
