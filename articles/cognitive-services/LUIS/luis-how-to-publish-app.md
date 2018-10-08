---
title: 예측 엔드포인트에 LUIS 앱 게시
titleSuffix: Azure Cognitive Services
description: 활성 LUIS 빌드와 테스트를 마치면 엔드포인트에 게시하여 클라이언트 응용 프로그램에 적용하세요.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 54d3b66f6a452e97e1d354fa75eb1b21065ab3e2
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031274"
---
# <a name="publish-your-trained-app"></a>학습된 앱 게시

활성 LUIS 빌드와 테스트를 마치면 엔드포인트에 게시하여 클라이언트 응용 프로그램에 적용하세요. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>게시

엔드포인트에 게시하려면 맨 위 오른쪽 패널에서 **게시**를 선택하세요. 

![위쪽, 오른쪽 탐색 모음](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

팝업 창이 표시되면 올바른 슬롯(스테이징 또는 프로덕션)을 선택합니다. 두 게시 슬롯을 사용하면 게시된 엔드포인트에 2가지 다른 버전이 있거나 2가지 다른 엔드포인트에 같은 버전이 있을 수 있습니다. 

앱은 LUIS 포털에 추가된 LUIS 리소스와 관련된 모든 지역에 게시됩니다. 예를 들어, [www.luis.ai](https://www.luis.ai)에서 만든 앱의 경우 **westus**에서 LUIS 리소스를 만든 후 앱에 리소스로 추가하면 이 앱은 해당 지역에 게시됩니다. LUIS 지역에 대한 자세한 내용은 [지역](luis-reference-regions.md)을 참조하세요.
 
![게시 팝업 창](./media/luis-how-to-publish-app/publish-pop-up.png)

앱이 성공적으로 게시되면 브라우저 위쪽에 녹색 성공 알림이 표시됩니다. 또한 녹색 알림 표시줄에는 엔드포인트에 대한 링크도 포함되어 있습니다. 

![게시 팝업 창](./media/luis-how-to-publish-app/publish-success.png)

엔드포인트 URL이 필요한 경우 링크를 선택하세요. 또한 맨 위 메뉴에서 **관리**를 선택하여 엔드포인트 URL로 이동한 다음, 왼쪽 메뉴에서 **키 및 엔드포인트**를 선택할 수도 있습니다. 

## <a name="configuring-publish-settings"></a>게시 설정 구성

맨 위 오른쪽 탐색에서 **관리**를 선택한 다음, **게시 설정**을 선택하여 게시 설정을 구성합니다. 

![게시 설정](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>감정 분석을 사용하도록 설정한 후 게시

<a name="enable-sentiment-analysis"></a>

감정 분석을 사용하면 LUIS에서 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)와 통합되어 감정 및 핵심 구 분석을 제공할 수 있습니다. 

Text Analytics 키를 제공할 필요는 없으며, 이 서비스에 대한 비용은 사용자의 Azure 계정에 부과되지 않습니다. 이 설정을 선택하면 지속됩니다. 

감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다.

감정 분석에서 JSON 엔드포인트의 응답에 대한 자세한 내용은 [감정 분석](luis-concept-data-extraction.md#sentiment-analysis)을 참조하세요.



## <a name="next-steps"></a>다음 단계

* LUIS에 대한 Azure 구독 키에 키를 추가하고 Bing Spell Check 키를 설정하고 결과에 모든 의도를 포함하는 방법은 [키 관리](./luis-how-to-manage-keys.md)를 참조하세요.
* 테스트 콘솔에서 게시된 앱을 테스트하는 방법에 대한 지침은 [앱 학습 및 테스트](luis-interactive-test.md)를 참조하세요.

