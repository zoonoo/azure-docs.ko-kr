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
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340199"
---
# <a name="conference-series-entity"></a>Conference Series 엔터티

<sub> *다음 특성은 Conference Series 엔터티에 한정됩니다. (Ty = '3') </sub>

이름    |설명                            |Type       | 작업
------- | ------------------------------------- | --------- | ----------------------------
Id      |엔터티 ID                              |Int64      |같음
CN      |컨퍼런스 시리즈의 정규화된 이름      |String     |같음
DCN     |컨퍼런스 시리즈 표시 이름         |String     |없음
CC      |컨퍼런스 시리즈의 총 인용 횟수         |Int32      |없음  
ECC     |컨퍼런스 시리즈의 총 예상 인용 횟수   |Int32      |없음
F.FId   |컨퍼런스 시리즈와 연결된 연구 분야 엔터티 ID |Int64  | 같음
F.FN    |컨퍼런스 시리즈와 연결된 연구 분야 이름  | Equals,<br/>StartsWith
