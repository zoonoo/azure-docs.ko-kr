---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025100"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [ **변칙 탐지기** 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) 를 클릭 합니다.
1. 필수 설정 모두 입력:

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기**를 클릭하고 리소스가 생성될 때까지 기다립니다. 만든 후에는 리소스 페이지로 이동 합니다.
1. 구성 된 `endpoint` 및 API 키를 수집 합니다.

    |포털의 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 다음과 유사 하 게 표시 됩니다. `https://westus2.api.cognitive.microsoft.com/`|
    |**키**|API 키|두 키의 1을 복사 합니다. 공백이 나 대시가 없는 32 영숫자 문자열입니다 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



