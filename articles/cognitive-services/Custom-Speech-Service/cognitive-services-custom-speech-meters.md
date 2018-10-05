---
title: 서비스 미터 및 견적 - Custom Speech Service
titlesuffix: Azure Cognitive Services
description: Azure의 Custom Speech Service 미터 및 견적에 대한 정보를 제공합니다.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 07/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 68fba1d6038a044336ba6aecd3ff163d29131b1d
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224701"
---
# <a name="custom-speech-service-meters-and-quotas"></a>Custom Speech Service 미터 및 견적

클라우드 기반 Custom Speech Service를 사용하면 음성-텍스트 전사에 대한 음성 모델을 사용자 지정할 수 있습니다.

Custom Speech Service를 사용하려면 [Custom Speech Service 포털](https://cris.ai)로 이동합니다.

현재 가격 책정 미터를 보려면 [Custom Speech Service의 Cognitive Services 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/) 페이지로 이동합니다.

## <a name="tiers-explained"></a>계층 설명
평가판 F0 계층은 테스트 및 프로토타입 용도에만 사용하는 것이 좋습니다. 프로덕션 시스템에는 S2 계층을 사용하는 것이 좋습니다. S2 계층을 사용하면 배포 규모를 시나리오에 필요한 SU(배율 단위) 수만큼 확장할 수 있습니다.

> [!NOTE]
> F0 계층과 S2 계층 간에는 마이그레이션이 *불가능*합니다.
>

## <a name="meters-explained"></a>미터 설명

### <a name="scale-out"></a>규모 확장
규모 확장은 새 가격 책정 모델에 새로 도입된 기능입니다. Scale Out을 사용하면 모델에서 처리할 수 있는 동시 요청 수를 제어할 수 있습니다.

**모델 배포 만들기** 보기에서 SU 측정을 사용하여 동시 요청을 설정할 수 있습니다. 자세한 내용은 [사용자 지정 음성-텍스트 엔드포인트 만들기](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md)를 참조하세요. 모델에서 사용할 것으로 예상되는 트래픽 양에 따라 적절한 수의 SU를 선택할 수 있습니다. 

> [!NOTE]
> 배율 단위마다 동시 요청 5개를 보장합니다. 필요에 따라 1개 이상을 구입할 수 있습니다. SU 수가 1씩 증가하므로 보장되는 동시 요청 수는 5개씩 증가합니다.
>

### <a name="log-management"></a>로그 관리
추가 비용을 부담하면 새로 배포된 모델에 대한 오디오 추적을 해제하도록 선택할 수 있습니다. Custom Speech Service는 모델의 오디오 요청 또는 기록을 로깅하지 않습니다.

## <a name="next-steps"></a>다음 단계
Custom Speech Service를 사용하는 방법에 대한 자세한 내용은 [Custom Speech Service 포털](https://cris.ai)을 참조하세요.

* [시작](cognitive-services-custom-speech-get-started.md)
* [FAQ](cognitive-services-custom-speech-faq.md)
* [용어](cognitive-services-custom-speech-glossary.md)
 