---
title: Academic Graph 엔터티 특성 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API에서 Academic Graph와 함께 사용할 수 있는 엔터티 특성에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705027"
---
# <a name="entity-attributes"></a>엔터티 특성

Academic Graph는 7가지의 엔터티 형식으로 구성됩니다. 모든 엔터티에는 엔터티 ID와 엔터티 형식이 있습니다.

## <a name="common-entity-attributes"></a>공용 엔터티 특성
이름    |Description                |형식       | 작업
------- | ------------------------- | --------- | ----------------------------
ID      |엔터티 ID                  |Int64      |같음
Ty      |엔터티 유형                |열거형   |같음

## <a name="entity-type-enum"></a>엔터티 형식 열거형
이름                                                            |value
----------------------------------------------------------------|-----
[문서](PaperEntityAttributes.md)                               |0
[작성자](AuthorEntityAttributes.md)                             |1
[저널](JournalEntityAttributes.md)                           |2
[컨퍼런스 시리즈](JournalEntityAttributes.md)                 |3
[컨퍼런스 인스턴스](ConferenceInstanceEntityAttributes.md)    |4
[소속](AffiliationEntityAttributes.md)                   |5
[연구 분야](FieldsOfStudyEntityAttributes.md)                      |6

