---
title: Language Understanding(LUIS) 경계에서 C#으로 LUIS 지역 찾기 | Microsoft Docs
description: LUIS에 대한 끝점 키 및 애플리케이션 ID를 사용하여 프로그래밍 방식으로 게시 지역을 찾습니다.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: f0df14736e0ed47957999e3aa7c6a22b0b0c0a35
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110413"
---
# <a name="region-can-be-determined-from-api-call"></a>지역은 API 호출을 기반으로 확인할 수 있음 
LUIS 앱 ID와 LUIS 구독 ID가 있는 경우 끝점 쿼리에 사용할 지역을 찾을 수 있습니다.

> [!NOTE] 
> 전체 C# 솔루션은 [**LUIS-Samples** Github 리포지토리](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/)에서 사용할 수 있습니다.

## <a name="luis-endpoint-query-strategy"></a>LUIS 끝점 쿼리 전략
각 LUIS 끝점 쿼리에는 다음이 필요합니다.

* 끝점 키
* 앱 ID
* 지역

LUIS 끝점 쿼리에서 올바른 끝점 키와 앱 ID를 사용하지만 잘못된 지역을 사용하는 경우 응답 코드는 401입니다. 401 요청은 구독 할당량으로 계산되지 않습니다. 이 요청을 모든 지역을 폴링하는 전략으로 전환하여 올바른 지역을 찾습니다. 올바른 지역은 2xx 상태 코드를 반환하는 유일한 요청입니다. 

|응답 코드|매개 변수|
|--|--|
|2xx|올바른 끝점 키<br>올바른 앱 ID<br>올바른 호스트 지역|
|401|올바른 끝점 키<br>올바른 앱 ID<br>‘잘못된’ 호스트 지역|

## <a name="c-class-code-to-find-region"></a>지역을 찾는 C# 클래스 코드
콘솔 애플리케이션은 LUIS 앱 ID와 끝점 키를 사용하며 이와 연결된 모든 지역을 반환합니다. 현재 끝점 키는 지역별로 만들어지므로 하나의 지역만 반환되어야 합니다.

.Net 라이브러리 종속성을 포함합니다.

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

지역을 찾도록 빌드된 이 사용자 지정 LUIS 클래스를 추가합니다. `luisAppId` 및 `luisSubscriptionKey`의 변수 값을 고유한 값으로 바꿉니다. 401을 반환하는 모든 지역이 디버그 콘솔에 기록됩니다. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

다음은 콘솔 응용 프로그램의 Main 메서드에서 사용자 지정 LUIS 클래스를 호출하는 예제입니다.

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

애플리케이션이 실행되면 콘솔에는 앱 ID에 해당하는 지역이 표시됩니다.

![LUIS 지역을 보여주는 콘솔 앱 스크린샷](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>다음 단계

LUIS [지역](luis-reference-regions.md)에 대해 자세히 알아봅니다.
