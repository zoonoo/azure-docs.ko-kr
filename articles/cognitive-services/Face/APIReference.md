---
title: API 참조-얼굴
titleSuffix: Azure Cognitive Services
description: API 참조는 Person, LargePersonGroup/PersonGroup, LargeFaceList/FaceList 및 얼굴 알고리즘 Api에 대 한 정보를 제공 합니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: reference
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 431525964eb69431600d36ba8249c6dc465a5df3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "76169923"
---
# <a name="face-api-reference-list"></a>Face API 참조 목록

Azure Face는 얼굴 감지 및 인식 알고리즘을 제공 하는 클라우드 기반 서비스입니다. Face API는 다음 범주로 구성됩니다.

- 얼굴 알고리즘 Api: [검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), 유사, [확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a), [식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) [찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)등의 핵심 기능을 다룹니다.
- [FaceList api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b): 유사 하 게 [찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)위해 FaceList를 관리 하는 데 사용 됩니다.
- [LargePersonGroup Person api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40): [id](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에 대 한 LargePersonGroup person 얼굴을 관리 하는 데 사용 됩니다.
- [LargePersonGroup api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d): [식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)을 위해 LargePersonGroup 데이터 집합을 관리 하는 데 사용 됩니다.
- [LargeFaceList api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc): 유사 하 게 [찾기](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)위해 LargeFaceList를 관리 하는 데 사용 됩니다.
- [PersonGroup Person api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c): [id](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에 대 한 PersonGroup person 얼굴을 관리 하는 데 사용 됩니다.
- [PersonGroup api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244): [식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)을 위해 PersonGroup 데이터 집합을 관리 하는 데 사용 됩니다.
- [스냅숏 api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-take): 구독 간 데이터 마이그레이션에 대 한 스냅숏을 관리 하는 데 사용 됩니다.
