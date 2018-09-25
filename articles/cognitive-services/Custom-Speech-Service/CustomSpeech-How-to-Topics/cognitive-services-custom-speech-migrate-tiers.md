---
title: Azure의 Custom Speech Service에서 엔드포인트의 가격 책정 계층 마이그레이션 | Microsoft Docs
description: Cognitive Services에서 Custom Speech Service 엔드포인트의 S0 및 S1 계층에서 S2 계층으로 배포를 마이그레이션하는 방법을 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 44411e0c92f4e24e274761821dd99efbe60c5f5d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964976"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>새 가격 책정 모델로 배포 마이그레이션
2017년 7월 현재, Custom Speech Service는 [새 가격 책정 모델](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)을 제공합니다. 새 모델은 *이해하기가 더 쉽고*, *비용 계산이 더 간단하며*, 크기 조정 면에서 *더 유연*합니다. 크기 조정의 경우 Microsoft는 배율 단위 개념을 도입했습니다. 각 배율 단위는 동시 요청 5개를 처리할 수 있습니다. 이전 모델에서 동시 요청에 대한 크기 조정은 S0 계층의 경우 동시 요청 5개, S1 계층의 경우 12개로 설정됐습니다. 사용 사례 요구 사항을 사용하여 더 큰 유연성을 제공하기 위해 이러한 제한을 시작했습니다.

이전 S0 또는 S1 계층을 실행하는 경우 기존 배포를 새 S2 계층으로 마이그레이션하는 것이 좋습니다. 새 S2 계층은 S0 및 S1 계층 둘 다 포함합니다. 다음 그림에서 사용 가능한 옵션을 확인할 수 있습니다.

![“가격 책정 계층 선택” 페이지](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Microsoft는 반자동 방식으로 마이그레이션을 다룹니다. 먼저, 새 가격 책정 계층을 선택하여 마이그레이션을 트리거합니다. 그런 다음, 사용자 배포를 자동으로 마이그레이션합니다.

이전 계층에서 배율 단위로 매핑이 다음 테이블에 표시됩니다.

| 계층 | 동시 요청(이전 모델) | 마이그레이션 | 동시 요청 |
|----- | ----- | ---- | ---- |
| S0 |  5   |   1개의 배율 단위를 가진 => **S2** |   5 |
| S1 |  12  |   3개의 배율 단위를 가진 => **S2** |  15 |

새 계층으로 마이그레이션하려면 다음을 수행합니다.

## <a name="step-1-check-your-existing-deployment"></a>1단계: 기존 배포 확인
[Custom Speech Service 포털](http://cris.ai)로 이동하여 기존 배포를 확인합니다. 예제에는 2개의 배포가 있습니다. 한 배포가 S0 계층에서 실행되면 다른 배포는 S1 계층에서 실행됩니다. 배포는 다음 테이블의 **배포 옵션** 열에 표시됩니다.

![배포 페이지](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>2단계: Azure Portal에서 새 가격 책정 계층 선택
1. 새 브라우저 탭을 열고 [Azure Portal](http://ms.portal.azure.com/)에 로그인합니다. 

2. **Cognitive Services** 창의 **구독** 목록에서 사용자 지정 음성 구독을 선택합니다. 

3. 구독 창에서 **가격 책정 계층**을 선택합니다.

    ![“가격 책정 계층” 링크](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. **가격 책정 계층 선택** 페이지에서 **S2 표준**을 선택합니다. 이 가격 책정 계층은 단순화되고 더욱 유연한 새 가격 책정 계층입니다.

5. **선택** 단추를 선택합니다.

    ![“가격 책정 계층 선택” 페이지](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>3단계: Custom Speech Service 포털에서 마이그레이션 상태 확인
Custom Speech Service 포털로 되돌아가 배포를 확인합니다. (브라우저 창이 아직 열려 있으면 새로 고칩니다.) 

관련 배포 상태가 *처리 중*으로 전환됐어야 합니다. **배포 옵션** 열을 확인하여 마이그레이션의 유효성을 검사할 수 있습니다. 이제 배율 단위 및 로깅에 대한 정보를 찾을 수 있습니다. 배율 단위는 이전 가격 책정 계층을 반영해야 합니다. 로깅은 테이블에 표시된 대로 켜져 있어야 합니다.

![배포 옵션 열](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> 마이그레이션 도중 문제가 발생하면 문의하세요.
>

## <a name="next-steps"></a>다음 단계
더 많은 자습서를 보려면 다음 항목을 참조하세요.
* [사용자 지정 음향 모델 만들기](cognitive-services-custom-speech-create-acoustic-model.md)
* [사용자 지정 언어 모델 만들기](cognitive-services-custom-speech-create-language-model.md)
* [사용자 지정 음성-텍스트 엔드포인트 만들기](cognitive-services-custom-speech-create-endpoint.md)
