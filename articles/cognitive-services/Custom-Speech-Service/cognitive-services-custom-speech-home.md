---
title: Azure에서 Custom Speech Service 개요 | Microsoft Docs
description: Custom Speech Service는 사용자가 음성-텍스트 전사에 대한 음성 모델을 사용자 지정할 수 있는 클라우드 기반 서비스입니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: da88989753069f7ba8ca2c2e2806a648f3df4e3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948369"
---
# <a name="what-is-custom-speech-service"></a>Custom Speech Service란?

Custom Speech Service는 사용자에게 음성-텍스트 전사에 대한 음성 모델을 사용자 지정할 수 있는 기능을 제공하는 클라우드 기반 서비스입니다.
Custom Speech Service를 사용하려면 [Custom Speech Service 포털](https://cris.ai)을 참조합니다.

Custom Speech Service는 응용 프로그램과 사용자에 맞게 조정된 사용자 지정 언어 모델과 음향 모델이 음성 모델을 만들 수 있습니다. Custom Speech Service에 특정 음성 및/또는 텍스트 데이터를 업로드하여 Microsoft의 기존 최신 음성 모델과 함께 사용할 수 있는 사용자 지정 모델을 만들 수 있습니다.

예를 들어 음성 상호 작용을 휴대폰, 태블릿 또는 PC 앱에 추가하는 경우 사용자 앱을 위해 특별히 설계된 음성 대 텍스트 엔드포인트를 만들려면 Microsoft의 음향 모델과 결합할 수 있는 사용자 지정 언어 모델을 만들 수 있습니다. 응용 프로그램을 특정 사용자 그룹 또는 특정 환경에서 사용하도록 설계한 경우 이 서비스를 사용하여 사용자 지정 음향 모델을 만들어 배포할 수 있습니다.


## <a name="how-do-speech-recognition-systems-work"></a>음성 인식 시스템은 어떻게 작동 하나요?
음성 인식 시스템은 함께 작동하는 여러 구성 요소로 구성됩니다. 가장 중요 한 구성 요소 두 가지는 음향 모델 및 언어 모델입니다.

음향 모델은 지정된 언어로 된 오디오의 짧은 조각을 많은 음소 또는 소리 단위의 하나로 레이블을 지정하는 분류자입니다. 예를 들어 "speech"라는 단어는 4개의 음소 "s p iy ch"로 구성됩니다. 이러한 분류는 대략 초당 100번 만들어집니다.

언어 모델은 단어의 시퀀스에 대한 확률 분포입니다. 언어 모델은 시스템이 단어 시퀀스 자체의 발생 가능성에 따라 비슷하게 들리는 단어의 시퀀스 중에서 결정하는 데 도움을 줍니다. 예를 들어 "recognize speech"와 "wreck a nice beach"는 비슷하게 들리지만 첫 번째 가설이 발생할 확률이 훨씬 더 높으므로 언어 모델에서 더 높은 점수를 할당합니다.

음향 및 언어 모델 모두는 학습 데이터에서 습득한 통계 모델입니다. 결과적으로 이 모델들은 응용 프로그램에서 사용할 때 발생하는 음성이 훈련 중 관찰된 데이터와 비슷한 경우 최상의 성능을 발휘합니다. Microsoft 음성 대 텍스트 엔진에서 음향 및 언어 모델은 막대한 음성 및 텍스트 컬렉션에 대해 학습됐으며, 스마트폰, 테블릿 또는 PC에서 Cortana와 상호작용하고, 음성으로 웹을 검색하거나 음성으로 텍스트 메시지를 친구에게 보내는 등의 가장 일반적인 사용 시나리오에 대한 최신 성능을 제공합니다.

## <a name="why-use-the-custom-speech-service"></a>Custom Speech Service를 사용하는 이유는?
Microsoft 음성 대 텍스트 엔진은 세계 정상급이면서 위에서 설명한 시나리오를 대상으로 합니다. 그러나 응용 프로그램에 대한 음성 쿼리에 특정 어휘 항목(예: 일반적인 말에서 드물게 나타나는 제품 이름 또는 전문 용어)이 포함될 것으로 예상하는 경우 언어 모델을 사용자 지정하여 향상된 성능을 얻을 수 있습니다.

예를 들어 음성으로 MSDN을 검색하는 앱을 빌드하는 경우 "개체 지향"이나 "네임스페이스" 또는 "닷넷"과 같은 용어가 일반적인 음성 응용 프로그램에서보다 더 빈번하게 나타날 수 있습니다. 언어 모델을 사용자 지정하면 시스템이 이러한 내용을 학습할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Custom Speech Service를 사용하는 방법에 대한 자세한 내용은 [Custom Speech Service 포털](https://cris.ai)을 참조합니다.

* [시작](cognitive-services-custom-speech-get-started.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
* [용어](cognitive-services-custom-speech-glossary.md)
