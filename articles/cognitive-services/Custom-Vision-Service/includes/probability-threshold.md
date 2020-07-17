---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "68423596"
---
**성능** 탭의 왼쪽 창에는 **확률 임계값** 슬라이더가 있습니다. 예측의 신뢰도 수준이 이 값에 도달해야만 올바른 것으로 간주됩니다(계산 정확도 및 회수 목적으로). 

확률 임계값이 높은 예측 호출은 정확도는 높지만 회수율이 낮은 결과를 반환하는 경향이 있습니다. 즉, 검색된 분류는 올바르지만 많은 분류가 검색되지 않습니다. 확률 임계값이 낮으면 그 반대입니다. 즉, 실제 분류가 대부분 검색되지만 해당 세트 내에 가양성이 더 많습니다. 이 점을 고려하여 프로젝트의 구체적인 요구에 따라 예측 임계값을 설정해야 합니다. 나중에 클라이언트 쪽에서 예측 결과를 수신할 때 여기서 사용한 값과 동일한 예측 임계값을 사용해야 합니다.