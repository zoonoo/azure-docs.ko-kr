---
title: LUIS 리소스 만들기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: a077d255648ff07cc88b43dece889a221c46c11f
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561513"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Azure Portal에서 LUIS 리소스 만들기

1. [이 링크](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)를 사용하여 Azure Portal에서 LUIS 리소스 만들기를 시작할 수 있습니다.

1. 필수 설정 모두 입력:

    |속성|목적|
    |--|--|
    |구독 | 리소스에 대해 요금이 청구되는 구독입니다.|
    |Resource group| 사용자가 선택하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다.|
    |속성| 사용자가 선택한 사용자 지정 이름입니다. 작성 및 예측 엔드포인트 쿼리에 대한 사용자 지정 하위 도메인으로 사용됩니다.|
    |작성 위치|모델과 연결된 지역입니다.|
    |작성 가격 책정 계층|초당 및 월별 최대 트랜잭션 수를 결정합니다.|
    |예측 위치|게시된 예측 엔드포인트 런타임과 연결된 지역입니다.|
    |예측 가격 책정 계층|초당 및 월별 최대 트랜잭션 수를 결정합니다.|

    > [!div class="mx-imgBorder"]
    > [![만들기 아래의 기본 사항 탭을 보여주는 스크린샷.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. **검토 + 만들기** 를 선택하고 리소스가 생성될 때까지 기다립니다.
1. 두 리소스를 모두 만든 후에도 Azure Portal에서 새 제작 리소스를 선택합니다. 그런 다음, **빠른 시작** 을 선택하여 프로그래밍 방식으로 작성하기 위한 제작 **엔드포인트 URL** 및 **키** 를 가져옵니다.

> [!TIP]
> 리소스를 사용하려면 LUIS 포털에서 [리소스를 할당합니다](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).
