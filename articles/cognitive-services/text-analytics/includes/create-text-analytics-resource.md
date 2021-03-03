---
title: Cognitive Services Text Analytics 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751002"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **리소스 만들기** 를 선택한 다음 **AI + Machine Learning** > 로 이동합니다.
   또는 [Create Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동 합니다.
1. 필요한 설정을 모두 입력합니다.

    |설정|값|
    |--|--|
    |이름|이름(2-64자)을 입력합니다.|
    |구독|적절한 구독을 선택합니다.|
    |위치|근처 위치를 선택합니다.|
    |가격 책정 계층| 표준 가격 책정 계층을 나타내는 **S** 를 입력합니다.|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택합니다.|

1. **만들기** 를 선택하고, 리소스가 만들어질 때까지 기다립니다. 자동으로 브라우저가 새로 만든 리소스 페이지로 리디렉션합니다.
1. 구성 된 `endpoint` 및 API 키를 수집 합니다.

    |포털의 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 와 유사 하 게 표시 됩니다 `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**키**|API 키|두 키 중 하나를 복사 합니다. <>는 공백이 나 대시가 없는 32 자리의 영숫자 문자열 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 입니다.|
