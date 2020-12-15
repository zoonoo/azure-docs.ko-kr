---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: d1add17c1c84d2a22d76aaa1f96aeca4db645ba7
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506363"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector 리소스 만들기

1. <a href="https://portal.azure.com" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>에 로그인 합니다.
1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">변칙 탐지기 <span class="docon docon-navigate-external x-hidden-focus"></span> 리소스 만들기</a> 를 선택 합니다.
1. 필수 설정 모두 입력:

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` -100 초당 호출 수, 초당 20K 트랜잭션. <br> 또는<br> `S0` 80-초당 초당 호출 수|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기** 를 클릭하고 리소스가 생성될 때까지 기다립니다. 만든 후에는 리소스 페이지로 이동 합니다.
1. 구성 된 `endpoint` 및 API 키를 수집 합니다.

    |포털의 키 및 끝점 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 다음과 유사 하 게 표시 됩니다. ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**키**|API 키|두 키의 1을 복사 합니다. 공백이 나 대시가 없는 32 영숫자 문자열입니다 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



