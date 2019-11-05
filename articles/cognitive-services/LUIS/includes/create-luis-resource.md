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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465953"
---
## <a name="create-a-luis-resource"></a>LUIS 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 만들기를 클릭 [ **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 모든 필수 설정을 입력 합니다.

    |설정|값|
    |--|--|
    |Name|원하는 이름 (2-64 자)|
    |구독|적절 한 구독 선택|
    |위치|주변 및 사용 가능한 위치를 선택 합니다.|
    |가격 책정 계층|`F0`-최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기** 를 클릭 하 고 리소스가 생성 될 때까지 기다립니다. 만든 후에는 리소스 페이지로 이동 합니다.
1. 구성 된 `endpoint` 및 API 키를 수집 하 고 [필요한 매개 변수 수집](#gathering-required-parameters)을 참조 하세요.

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
