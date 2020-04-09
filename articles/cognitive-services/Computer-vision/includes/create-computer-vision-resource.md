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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876093"
---
## <a name="create-an-computer-vision-resource"></a>컴퓨터 비전 리소스 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 컴퓨터 비전 리소스 [만들기를 **클릭합니다.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. 필수 설정 모두 입력:

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기**를 클릭하고 리소스가 생성될 때까지 기다립니다. 생성된 후 리소스 페이지로 이동합니다.
1. 구성된 `{ENDPOINT_URI}` 수집 `{API_KEY}`및 세부 정보 수집 에 필요한 [매개 변수 를 참조합니다.](../computer-vision-how-to-install-containers.md#gathering-required-parameters)
