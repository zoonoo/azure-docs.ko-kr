---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499089"
---
## <a name="create-an-computer-vision-resource"></a>Computer Vision 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [ **Computer Vision** 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) 를 클릭 합니다.
1. 모든 필수 설정을 입력 합니다.

    |설정|값|
    |--|--|
    |Name|원하는 이름 (2-64 자)|
    |구독|적절 한 구독 선택|
    |위치|주변 및 사용 가능한 위치를 선택 합니다.|
    |가격 책정 계층|`F0`-최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기** 를 클릭 하 고 리소스가 생성 될 때까지 기다립니다. 만든 후에는 리소스 페이지로 이동 합니다.
1. 구성 된 `{ENDPOINT_URI}` 및 `{API_KEY}`를 수집 하 고 자세한 내용은 [필수 매개 변수 수집](../computer-vision-how-to-install-containers.md#gathering-required-parameters) 을 참조 하세요.
