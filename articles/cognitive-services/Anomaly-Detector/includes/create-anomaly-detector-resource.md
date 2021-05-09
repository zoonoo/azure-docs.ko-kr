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
ms.openlocfilehash: feb79d047a6c3b25176a13dcc3c3afd53a51459e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102445284"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector 리소스 만들기

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다.
1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">Anomaly Detector 만들기</a> 리소스를 선택합니다.
1. 필수 설정 모두 입력:

    |설정|값|
    |--|--|
    |속성|원하는 이름(2-64자)|
    |Subscription|적합한 구독 선택|
    |위치|주변 및 사용 가능한 위치 선택|
    |가격 책정 계층|`F0` - 초당 호출 10회, 월별 20K 트랜잭션. <br> 또는<br> `S0` - 초당 80회 호출|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기** 를 클릭하고 리소스가 생성될 때까지 기다립니다. 생성된 후 리소스 페이지로 이동합니다.
1. 구성된 `endpoint` 및 API 키를 수집합니다.

    |포털의 키 및 엔드포인트 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|엔드포인트를 복사합니다. ` https://<your-resource-name>.cognitiveservices.azure.com/`과 비슷합니다.|
    |**키**|API 키|두 키 중 하나를 복사합니다. 공백 또는 대시가 없는 32자리 영숫자 문자열인 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`입니다.|



