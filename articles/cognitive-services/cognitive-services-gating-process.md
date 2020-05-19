---
title: Cognitive Services 제어 프로세스
titleSuffix: Azure Cognitive Services
description: 새 Cognitive Services 컨테이너 및 Api에 대 한 초기 액세스를 적용 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599596"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Azure Cognitive Services에 대 한 제어 프로세스

> [!NOTE]
> 서비스 제공이 제어 되는 미리 보기를 완료 한 후에는 액세스를 위한 응용 프로그램이 필요 하지 않은 "제어 되지 않은" 공개 미리 보기로 이동 합니다. 미리 보기 프로세스 후에는 GA (일반 공급)로 릴리스됩니다.

새로운 Azure Cognitive Services 제품이 도입 됨에 따라 고객이 응용 프로그램을 통해 액세스를 요청 해야 하는 제어 된 미리 보기를 거칩니다. 이 제어 프로세스는 광범위 하 게 사용할 수 있는 서비스 제공에 대 한 개선 기회를 식별 하는 데 도움이 됩니다. 

이 문서는 제어 된 Cognitive Services 제품에 대 한 응용 프로그램 프로세스를 안내 합니다.

## <a name="eligibility-and-approval-process"></a>자격 및 승인 프로세스

제어 프로세스는 관심을 측정 하 고 고객의 요구를 더 잘 이해할 수 있도록 준비 되어 있습니다. Microsoft 팀은 유효한 Azure 구독 및 유효한 비즈니스 시나리오를 사용 하 여 Microsoft 상업적 고객의 [응용 프로그램](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) 을 승인 합니다. 고객은 다음과 같은 경우 응용 프로그램을 거부할 수 있습니다.

 - 어떤 조직과도 연결 되지 않습니다.
 - 유효한 Azure 구독이 없습니다.
 - 응용 프로그램이 개인 전자 메일 ( @hotmail.com , @gmail.com , @yahoo.com )을 통해 제출 되었습니다.
 - 적절 한 근거 또는 비즈니스 시나리오가 제공 되지 않았습니다.

여러 고객 부문에서 수요를 고려 하 여 승인 프로세스를 신속 하 게 시도 합니다. 그러나 타임 라인에 커밋할 수 없습니다. 결정이 되 면 Microsoft 팀은 다음 단계를 위해 사용자와 계정 관리 팀에 연락 합니다. 이해 하 고 있는 동안 감사 합니다.

응용 프로그램이 승인 되 면 Microsoft 팀이 세부 정보, 설명서 및 지침이 포함 된 전자 메일을 보냅니다. Cognitive Services 가격 정보는 [여기](https://azure.microsoft.com/pricing/details/cognitive-services/)에서 제공 됩니다.


현재 아래 서비스는 제어 프로세스를 통해 제공 됩니다.

## <a name="gated-web-apis"></a>제어 된 웹 Api

|서비스  |
|---------|
|변칙 탐지기 v2     | 

## <a name="gated-online-containers"></a>제어 된 온라인 컨테이너

| 서비스                             | 컨테이너                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Computer Vision][cv-containers]    | 읽기                                                                          |
| [Face][fa-containers]               | Face                                                                          |
| [Form Recognizer][fr-containers]    | Form Recognizer                                                               |
| [Speech Service API][sp-containers] | 음성 텍스트 (사용자 지정 및 표준) 및 텍스트 음성 변환 (사용자 지정 및 표준) |
| [Translator Text][tt-containers]    | Translator Text                                                               |

> [!IMPORTANT]
> 서비스 또는 컨테이너 화 된 제품이 나열 되지 않은 경우에는 제어 되지 않거나 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [제어 된 서비스 등록](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
