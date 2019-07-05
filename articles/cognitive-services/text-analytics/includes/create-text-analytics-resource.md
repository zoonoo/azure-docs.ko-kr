---
title: 컨테이너 지원
titleSuffix: Azure Cognitive Services
description: Cognitive services 텍스트 분석 리소스를 만드는 방법에 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455144"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 선택 **+ 리소스 만들기**, 이동할 **AI + 기계 학습 > Text Analytics**를 클릭 하거나 [만들기 **텍스트 분석**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. 모든 필수 설정을 입력 합니다.

    |설정|값|
    |--|--|
    |이름|원하는 이름 (2 ~ 64 자)|
    |구독|적절 한 구독을 선택 합니다.|
    |Location|가까운 위치를 선택 합니다.|
    |가격 책정 계층|`S` -표준 가격 책정 계층|
    |리소스 그룹|사용 가능한 리소스 그룹을 선택 합니다.|

1. 클릭 **만들기** 만들려는 리소스를 기다립니다. 브라우저를 새로 만든된 리소스 페이지로 자동으로 리디렉션합니다를 만든 후
1. 수집 구성 `endpoint` 및 API 키:

    |포털에서 리소스 탭|설정|값|
    |--|--|--|
    |**개요**|엔드포인트|끝점을 복사 합니다. 유사 하 게 표시 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**키**|API 키|1의 두 키를 복사 합니다. 공백이 나 대시 없이 32 자리 영숫자 문자열 `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`합니다.|