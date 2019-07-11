---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 52d8e1355558b197b193a50c7cde571799541268
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717185"
---
## <a name="create-a-luis-resource"></a>LUIS 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 클릭 [만들 **Language Understanding**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS)
1. 모든 필수 설정을 입력 합니다.

    |설정|값|
    |--|--|
    |이름|원하는 이름 (2 ~ 64 자)|
    |구독|적절 한 구독을 선택 합니다.|
    |위치|모든 인접 및 사용할 수 있는 위치를 선택 합니다.|
    |가격 책정 계층|`F0` -최소한의 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택 합니다.|

1. 클릭 **만들기** 만들려는 리소스를 기다립니다. 를 만든 후 리소스 페이지로 이동
1. 수집 구성 `endpoint` 및 API 키:

    |포털에서 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 유사 하 게 표시 `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**키**|API 키|1의 두 키를 복사 합니다. 공백이 나 대시 없이 32 자리 영숫자 문자열 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`합니다.|
