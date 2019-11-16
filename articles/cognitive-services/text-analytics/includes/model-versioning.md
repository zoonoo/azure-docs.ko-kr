---
title: 모델 버전 관리
titleSuffix: Azure Cognitive Services
description: V3 엔드포인트에서 모델 버전 지정
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021138"
---
Text Analytics API 버전 3을 사용하여 데이터에 사용되는 Text Analytics 모델을 선택할 수 있습니다. 선택적 `model-version` 매개 변수를 사용하여 요청에서 모델의 버전을 선택합니다. 이 매개 변수를 지정하지 않을 경우 API는 안정적인 최신 모델 버전 `latest`를 기본값으로 설정합니다.

사용 가능한 모델 버전:
* `2019-10-01`(`latest`)

V3 엔드포인트의 각 응답에는 사용된 모델 버전을 지정하는 `model-version` 필드가 포함됩니다.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
