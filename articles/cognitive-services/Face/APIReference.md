---
title: API 참조 - Face API
titleSuffix: Azure Cognitive Services
description: API 참조는 사용자, LargePersonGroup/PersonGroup, LargeFaceList/FaceList 및 얼굴 알고리즘 Api에 대 한 정보를 제공합니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: reference
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f4258f34bb7d353ee4e76f4675f4ef672a4a8c78
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547608"
---
# <a name="api-reference"></a>API 참조

Azure Face API는 얼굴 감지 및 인식에 대한 알고리즘을 제공하는 클라우드 기반 API입니다. Face API는 다음 범주로 구성됩니다.

- 알고리즘 face Api: 와 같은 핵심 기능을 다룹니다 [검색](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [유사한 찾을](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)를 [확인](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a), [식별](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), 및 [그룹](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
- [FaceList Api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b): [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)에 대한 FaceList를 관리하는 데 사용됩니다.
- [LargePersonGroup Person Api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40): [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에 대한 LargePersonGroup Person Faces를 관리하는 데 사용됩니다.
- [LargePersonGroup Api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d): [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에 대한 LargePersonGroup 데이터 세트를 관리하는 데 사용됩니다.
- [LargeFaceList Api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc): [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)에 대한 LargeFaceList를 관리하는 데 사용됩니다.
- [PersonGroup Person Api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c): [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에 대한 PersonGroup Person Faces를 관리하는 데 사용됩니다.
- [PersonGroup Api](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244): [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)에 대한 PersonGroup 데이터 세트를 관리하는 데 사용됩니다.
- [Api를 스냅숏](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-take): 여러 구독 간 데이터 마이그레이션용 스냅샷을 관리하는 데 사용됩니다.
