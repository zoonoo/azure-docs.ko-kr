---
title: Emotion API란?
titlesuffix: Azure Cognitive Services
description: 클라우드 기반 감정 인식 알고리즘을 사용하여 더욱 개인화된 앱을 빌드합니다.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: abf94e0ab6ebb3df649b1958503c086feb4fa19e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237129"
---
# <a name="what-is-the-emotion-api"></a>Emotion API란?

> [!IMPORTANT]
> Emotion API는 2019년 2월 15일부터 더 이상 사용되지 않습니다. 이제 감정 인식 기능은 [Face API](https://docs.microsoft.com/azure/cognitive-services/face/)의 일부로 일반 공급됩니다. 

Microsoft 클라우드 기반 감정 인식 알고리즘을 사용하여 보다 개인 설정된 앱을 빌드할 수 있는 Microsoft Emotion API를 시작합니다.

### <a name="emotion-recognition"></a>감정 인식

Emotion API 베타는 이미지를 입력으로 사용하고, 이미지에 있는 각 얼굴의 감정 집합에 대한 신뢰도를 반환하고, Face API를 통해 얼굴의 경계 상자를 반환합니다. 감지되는 감정은 행복, 슬픔, 놀람, 분노, 두려움, 경멸, 역겨움 또는 중립입니다. 이러한 감정은 Emotion API를 통해 식별된 동일한 기본 얼굴 표정을 통해, 문화에 관계없이 전체적으로 전달됩니다.

**결과 해석:**

Emotion API의 결과를 해석하는 경우 점수가 단일 점수로 합산되도록 정규화되기 때문에 가장 높은 점수를 가진 감정으로 해석되어야 합니다. 사용자는 필요에 따라 애플리케이션 내에서 더 높은 신뢰도 임계값을 설정할 수 있습니다.

감정 감지에 대한 자세한 내용은 API 참조를 참조하세요.
  * 기본: 사용자가 이미 Face API를 호출한 경우 얼굴 사각형을 입력으로 제출하고 기본 계층을 사용할 수 있습니다. [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * 표준: 사용자가 얼굴 사각형을 제출하지 않는 경우 표준 모드를 사용해야 합니다.  [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Emotion API를 사용하여 스트리밍 비디오를 해석하는 방법에 대한 샘플은 [실시간으로 비디오를 분석하는 방법](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion)을 참조하세요.
