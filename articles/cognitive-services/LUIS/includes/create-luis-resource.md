---
title: LUIS 리소스 만들기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465953"
---
## <a name="create-a-luis-resource"></a>LUIS 리소스 만들기

1. [Azure 포털에](https://portal.azure.com) 로그인
1. [**Language Understanding 클라이언트 만들기**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 클릭
1. 필요한 모든 설정을 입력합니다.

    |설정|값|
    |--|--|
    |이름|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기**를 클릭하고 리소스가 생성될 때까지 기다립니다. 생성된 후 리소스 페이지로 이동합니다.
1. 구성된 API `endpoint` 키를 수집하려면 [필요한 매개 변수 수집을](#gathering-required-parameters)참조하십시오.

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
