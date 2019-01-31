---
title: Azure에서 Custom Speech Service로 사용자 지정 음성 엔드포인트 만들기 | Microsoft Docs
description: Cognitive Services에서 Custom Speech Service를 통해 사용자 지정 음성-텍스트 엔드포인트를 만드는 방법을 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 07/08/2017
ms.author: panosper
ms.openlocfilehash: f9aca8529bf0407022ff3683c1cd73b87045f2f2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216675"
---
# <a name="create-a-custom-speech-to-text-endpoint"></a>사용자 지정 음성-텍스트 엔드포인트 만들기

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

사용자 지정 음향 모델 또는 언어 모델을 만든 후에 사용자 지정 음성-텍스트 변환 엔드포인트에 배포할 수 있습니다. 

## <a name="create-an-endpoint"></a>엔드포인트 만들기
새 사용자 지정 엔드포인트를 만들려면 페이지 맨 위에 있는 **Custom Speech** 메뉴에서 **배포**를 선택합니다. 그러면 현재 사용자 지정 엔드포인트 표를 볼 수 있는 **배포** 페이지로 이동됩니다. 아직 엔드포인트를 만들지 않은 경우 표가 비어 있습니다. 현재 로캘이 표 제목에 반영됩니다. 

다른 언어에 대한 배포를 만들려면 **로캘 변경**을 클릭합니다. 지원되는 언어에 대한 자세한 내용은 [Custom Speech Service에서 지원되는 로캘](cognitive-services-custom-speech-change-locale.md)을 참조하세요.

새 엔드포인트를 만들려면 **새로 만들기**를 선택합니다. **배포 만들기** 창에서 사용자 지정 배포의 **이름** 및 **설명** 상자에 정보를 입력합니다.

**구독** 콤보 상자에서 사용할 구독을 선택합니다. S2 구독인 경우 배율 단위 및 콘텐츠 로깅을 선택할 수 있습니다. 배율 단위 및 로깅에 대한 자세한 내용은 [Custom Speech Service 미터 및 견적](../cognitive-services-custom-speech-meters.md)을 참조하세요.

다음 표는 배율 단위가 사용 가능한 동시 요청에 매핑되는 방식을 보여줍니다.

| 배율 단위 | 동시 요청 수 |
| ------ | ----- |
| 0 | 1 |
| 1 | 5 |
| 2 | 10 |
| 3 | 15 |
| n | 5 * n |

콘텐츠 로깅을 켜거나 끌 수도 있습니다. 즉, Microsoft 내부용으로 사용할 수 있도록 엔드포인트 트래픽을 저장할 것인지 여부를 선택할 수 있습니다. 선택하지 않으면 트래픽이 저장되지 않습니다. 콘텐츠 로깅을 사용하지 않으면 추가 비용이 발생합니다. 자세한 내용은 [가격 책정 정보 페이지](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)를 참조하세요.

> [!NOTE]
> 가격 책정 페이지에서는 콘텐츠 로깅을 "추적 없음"으로 부릅니다.
>


또한 배율 단위 비용 및 콘텐츠 로깅에 미치는 영향을 알 수 있도록 Microsoft에서 대략적인 예상 비용 정보를 제공합니다. 이 비용은 예상 값이며 실제 비용과 다를 수 있습니다.

> [!NOTE]
> 이러한 설정은 F0(평가판 계층) 구독에서 사용할 수 없습니다.
>

**음향 모델** 목록에서 원하는 음향 모델을 선택하고, **언어 모델** 목록에서 원하는 언어 모델을 선택합니다. 음향 모델 및 언어 모델 선택 항목에는 항상 기본 Microsoft 모델이 포함됩니다. 기본 모델을 선택하면 조합이 제한됩니다. 대화형 기본 모델을 검색 및 받아쓰기 기본 모델과 혼합할 수 없습니다.

![배포 만들기 페이지](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-create2.png)

> [!NOTE]
> 확인란을 선택하여 사용 약관 및 가격 책정 정보에 동의해야 합니다.
>

음향 및 언어 모델을 선택한 후 **만들기**를 선택합니다. 그러면 **배포** 페이지로 돌아갑니다. 이제 새 엔드포인트에 해당하는 항목이 표에 포함되어 있습니다. 이 엔드포인트의 상태는 생성되는 동안 현재 상태를 반영합니다. 사용자 지정 모델을 사용하여 새 엔드포인트를 인스턴스화하는 데 최대 30분이 걸릴 수 있습니다. 배포 상태가 *완료*이면 엔드포인트를 사용할 준비가 완료된 것입니다.

![배포 페이지](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-ready.png)

배포 준비가 완료되면 배포 이름이 링크로 변합니다. 이 링크를 선택하면 웹 소켓을 사용하는 HTTP 요청 또는 Microsoft Cognitive Services Speech 클라이언트 라이브러리에 사용할 사용자 지정 엔드포인트 URL을 표시하는 **배포 정보** 페이지가 나타납니다.

![배포 정보 페이지](../../../media/cognitive-services/custom-speech-service/custom-speech-deployment-info2.png)

## <a name="next-steps"></a>다음 단계

더 많은 자습서를 보려면 다음 항목을 참조하세요.
* [사용자 지정 음성-텍스트 엔드포인트 사용](cognitive-services-custom-speech-use-endpoint.md)
* [사용자 지정 음향 모델 만들기](cognitive-services-custom-speech-create-acoustic-model.md)
* [사용자 지정 언어 모델 만들기](cognitive-services-custom-speech-create-language-model.md)
