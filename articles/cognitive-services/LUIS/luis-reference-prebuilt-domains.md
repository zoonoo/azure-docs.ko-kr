---
title: 미리 빌드된 도메인 참조-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding Intelligent Services)에서 미리 작성된 의도 및 엔터티 컬렉션에 해당하는 미리 작성된 도메인에 대한 참조입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 6e701396191ec8bf66ece0e2858b9f32857e7b17
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933594"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS 앱에 대한 미리 작성된 도메인 참조
이 참조는 LUIS에서 제공하는 의도 및 엔터티의 미리 작성된 컬렉션에 해당하는 [미리 작성된 도메인](luis-how-to-use-prebuilt-domains.md)에 대한 정보를 제공합니다.

반대로, [사용자 지정 도메인](luis-how-to-start-new-app.md)은 의도 및 모델 없이 시작합니다. 사용자 지정 모델에 미리 작성된 도메인 의도 및 엔터티를 추가할 수 있습니다.

## <a name="supported-domains-across-cultures"></a>문화권에서 지원 되는 도메인

유일 하 게 지원 되는 문화권은 영어입니다. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|엔터티 유형|description|
|--|--|
|일정|일정은 개인 모임 및 약속에 대 한 것으로, 전 세계 컵 일정, 시애틀 이벤트 달력 등의 공용 이벤트 나 일반 달력 (예: 오늘날의 일, 일은 노동절 일 경우 시작 하는 것)입니다.|
|통신|호출을 수행 하 고, 텍스트 또는 인스턴트 메시지를 보내고, 연락처와 기타 다양 한 통신 관련 요청 (일반적으로 보냄)을 찾아 추가 하도록 요청 합니다. 연락처 이름만 쿼리가 통신 도메인에 속하지 않습니다.|
|EMail|Email은 통신 도메인의 하위 도메인입니다. 주로 전자 메일을 통해 메시지를 보내고 받는 요청을 포함 합니다.|
|HomeAutomation|HomeAutomation 도메인은 스마트 홈 장치를 제어 하는 것과 관련 된 의도 및 엔터티를 제공 합니다. 주로 광원 및 공기 공조 관련 된 제어 명령을 지원 하지만 다른 전기 어플라이언스에 대 한 일반화 기능이 있습니다.|
|참고|참고 도메인은 사용자를 위해 메모를 작성 하 고 항목을 기록 하기 위한 의도 및 엔터티를 제공 합니다.|
|장소|장소에는 비즈니스, 기관, 식당, 공공 공간 및 주소가 포함 됩니다. 도메인은 위치, 운영 시간 및 거리와 같은 공공 장소에 대 한 정보를 찾고 요청할 수 있는 위치를 지원 합니다.|
|RestaurantReservation|식당 예약 도메인은 식당 예약 처리에 대 한 의도를 지원 합니다.|
|ToDo|ToDo 도메인은 사용자가 할 일 항목을 추가, 표시 및 삭제할 수 있는 작업 목록 유형을 제공 합니다.|
|공공 시설|유틸리티 도메인은 일반적인 의도 및 길이 발언를 포함 하는 모든 LUIS 미리 작성 된 모델 중에서 일반적인 도메인입니다.|
|Weather|날씨 도메인은 위치 및 시간을 기준으로 날씨 조건 및 권고를 확인 하는 데 중점을 둔 시간을 확인 하거나 시간을 확인 합니다.|
|Web|웹 도메인은 웹 사이트를 검색 하기 위한 의도 및 엔터티를 제공 합니다.|
