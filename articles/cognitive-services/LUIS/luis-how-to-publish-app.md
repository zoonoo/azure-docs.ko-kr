---
title: 앱 게시 - LUIS
titleSuffix: Azure Cognitive Services
description: 활성 LUIS 빌드와 테스트를 마치면 엔드포인트에 게시하여 클라이언트 애플리케이션에 적용하세요.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053429"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>스테이징 또는 프로덕션 엔드포인트에 활성 상태의 학습된 앱 게시

활성 LUIS 앱 빌드, 교육 및 테스트를 완료하면 끝점에 게시하여 클라이언트 응용 프로그램에서 사용할 수 있도록 합니다. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>게시

1. 엔드포인트에 게시하려면 맨 위 오른쪽 패널에서 **게시**를 선택하세요. 

    ![상단, 오른쪽 탐색 표시줄에 버튼 게시](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. 게시된 예측 끝점에 대한 설정을 선택한 다음 **게시를**선택합니다.

    ![게시 설정을 선택한 다음 게시 단추를 선택합니다.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>게시 슬롯

팝업 창에 올바른 슬롯을 선택합니다. 

* 스테이징
* 프로덕션 

두 게시 슬롯을 모두 사용하면 게시된 끝점에서 두 가지 버전의 앱을 사용할 수 있거나 두 개의 서로 다른 끝점에서 동일한 버전을 사용할 수 있습니다. 

### <a name="publishing-regions"></a>게시 지역

앱은 Azure 리소스 **관리** -> 페이지에서 LUIS 포털에 추가된 LUIS 예측 끝점 리소스와 연결된 모든 지역에**[게시됩니다.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

예를 들어 [www.luis.ai](https://www.luis.ai)만든 앱의 경우 **westus** 및 **eastus의**두 리전에서 LUIS 리소스를 만들고 이를 리소스로 앱에 추가하면 앱이 두 리전에 모두 게시됩니다. LUIS 지역에 대한 자세한 내용은 [지역](luis-reference-regions.md)을 참조하세요.

> [!TIP]
> 3개의 작성 영역이 있습니다. 게시하려는 리전에서 작성해야 합니다. 모든 지역에 게시해야 하는 경우 3개 저작 지역에서 작성 프로세스와 결과 학습된 모델을 관리해야 합니다. 


## <a name="configuring-publish-settings"></a>게시 설정 구성

슬롯을 선택한 후 다음에 대한 게시 설정을 구성합니다.

* 감정 분석
* 맞춤법 수정 - v2 예측 끝점만
* 음성 초기화 

게시한 후 이러한 설정은 **관리** 섹션의 **게시 설정** 페이지에서 검토할 수 있습니다. 모든 게시에 따라 설정을 변경할 수 있습니다. 게시를 취소하면 게시 중에 변경한 내용도 취소됩니다. 

### <a name="when-your-app-is-published"></a>앱이 게시되는 경우

앱이 성공적으로 게시되면 성공 알림이 브라우저 상단에 표시됩니다. 알림에는 끝점에 대한 링크도 포함됩니다. 

엔드포인트 URL이 필요한 경우 링크를 선택하세요. 상단 메뉴에서 **관리를** 선택한 다음 왼쪽 메뉴에서 **Azure 리소스를** 선택하여 끝점 URL로 이동하면 됩니다. 

## <a name="sentiment-analysis"></a>감정 분석

<a name="enable-sentiment-analysis"></a>

감정 분석을 사용하면 LUIS에서 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)와 통합되어 감정 및 핵심 구 분석을 제공할 수 있습니다. 

Text Analytics 키를 제공할 필요는 없으며, 이 서비스에 대한 비용은 사용자의 Azure 계정에 부과되지 않습니다. 

감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다. `positive`, `neutral` 및 `negative`의 감정 레이블은 지원되는 문화를 따릅니다. 현재 영어만 감정 레이블을 지원합니다. 

감정 분석에서 JSON 엔드포인트의 응답에 대한 자세한 내용은 [감정 분석](luis-concept-data-extraction.md#sentiment-analysis)을 참조하세요.

## <a name="spelling-correction"></a>맞춤법 교정

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

철자에 대한 수정은 LUIS 사용자 발언 예측 전에 이루어집니다. 응답에서 맞춤법을 포함하여 원래 발언의 변경 내용을 볼 수 있습니다.

## <a name="speech-priming"></a>음성 초기화

음성 프라이밍은 텍스트를 음성으로 변환하기 전에 LUIS 모델을 음성 서비스로 보내는 프로세스입니다. 이를 통해 음성 서비스는 모델에 대해 음성 변환을 보다 정확하게 제공할 수 있습니다. 이렇게 하면 한 번의 음성 통화를 하고 LUIS 응답을 다시 가져오기 방식으로 봇 음성 및 LUIS 요청 및 응답을 한 번의 통화로 수행할 수 있습니다. 전체적으로 대기 시간이 줄어듭니다.

## <a name="next-steps"></a>다음 단계

* LUIS에 대한 Azure 구독 키에 키를 추가하고 Bing Spell Check 키를 설정하고 결과에 모든 의도를 포함하는 방법은 [키 관리](./luis-how-to-azure-subscription.md)를 참조하세요.
* 테스트 콘솔에서 게시된 앱을 테스트하는 방법에 대한 지침은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.

