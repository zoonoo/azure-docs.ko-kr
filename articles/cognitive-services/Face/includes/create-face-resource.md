---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878371"
---
## <a name="create-an-face-resource"></a>얼굴 리소스 만들기

1. [Azure 포털에](https://portal.azure.com) 로그인
1. [얼굴 만들기 리소스를 **클릭합니다.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. 필수 설정 모두 입력:

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기**를 클릭하고 리소스가 생성될 때까지 기다립니다. 생성된 후 리소스 페이지로 이동합니다.
1. 구성된 `endpoint` API 키 수집:

    |포털의 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사합니다. 그것은 비슷합니다.`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**구성**|API 키|두 키 중 1개를 복사합니다. 공백이나 대시가없는 32 영숫자 문자 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`문자열입니다.|
