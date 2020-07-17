---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422308"
---
## <a name="create-a-speech-resource"></a>음성 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. [ **음성** 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) 를 클릭 합니다.
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

    |포털의 리소스 탭|설정|Value|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 다음과 유사 하 게 표시 됩니다.`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**키**|API 키|두 키의 1을 복사 합니다. 공백이 나 대시가 없는 32 영숫자 문자열입니다 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
