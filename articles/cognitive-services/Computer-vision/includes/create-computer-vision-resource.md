---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876093"
---
## <a name="create-an-computer-vision-resource"></a>Computer Vision 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [ **Computer Vision** 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) 를 클릭 합니다.
1. 필수 설정 모두 입력:

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기**를 클릭하고 리소스가 생성될 때까지 기다립니다. 생성된 후 리소스 페이지로 이동합니다.
1. 구성 된 `{ENDPOINT_URI}` 및 `{API_KEY}` 를 수집 하 고 자세한 내용은 [필수 매개 변수 수집](../computer-vision-how-to-install-containers.md#gathering-required-parameters) 을 참조 하세요.
