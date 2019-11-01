---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72822010"
---
## <a name="create-a-luis-resource"></a>LUIS 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 만들기를 클릭 [ **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. 모든 필수 설정을 입력 합니다.

    |설정|Value|
    |--|--|
    |name|원하는 이름 (2-64 자)|
    |Subscription|적절 한 구독 선택|
    |위치|주변 및 사용 가능한 위치를 선택 합니다.|
    |가격 책정 계층|`F0`-최소 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹 선택|

1. **만들기** 를 클릭 하 고 리소스가 생성 될 때까지 기다립니다. 만든 후에는 리소스 페이지로 이동 합니다.
1. 구성 된 `endpoint` 및 API 키를 수집 합니다.

    |포털의 리소스 탭|설정|Value|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 다음과 같이 표시 됩니다 `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**키**|API 키|두 키의 1을 복사 합니다. `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`공백이 나 대시가 없는 32 영숫자 문자열입니다.|
