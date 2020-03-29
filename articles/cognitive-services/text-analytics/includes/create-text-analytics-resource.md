---
title: 코그너티브 서비스 텍스트 분석 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383429"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>코그너티브 서비스 텍스트 분석 리소스 만들기

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. **리소스 만들기를**선택한 다음 **AI + 기계 학습** > **텍스트 분석으로**이동합니다.
   또는 텍스트 [분석 만들기로](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)이동합니다.
1. 필요한 모든 설정을 입력합니다.

    |설정|값|
    |--|--|
    |이름|이름(2-64자)을 입력합니다.|
    |Subscription|적절한 구독을 선택합니다.|
    |위치|주변 위치를 선택합니다.|
    |가격 책정 계층| **S**, 표준 가격 책정 계층을 입력합니다.|
    |Resource group|사용 가능한 리소스 그룹을 선택합니다.|

1. **을 만들고**리소스가 만들어질 때까지 기다립니다. 브라우저가 새로 만든 리소스 페이지로 자동으로 리디렉션됩니다.
1. 구성된 API `endpoint` 키를 수집합니다.

    |포털의 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사합니다. 와 비슷하게 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`보입니다.|
    |**키**|API 키|두 키 중 하나를 복사합니다. 공백이나 대시가 없는 32자 영숫자 문자열입니다: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
