---
title: 미리 작성된 도메인 참조
titleSuffix: Azure
description: LUIS(Language Understanding Intelligent Services)에서 미리 작성된 의도 및 엔터티 컬렉션에 해당하는 미리 작성된 도메인에 대한 참조입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3265477108b7e74d65050408add6c5d5c94b4852
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233885"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS 앱에 대한 미리 작성된 도메인 참조
이 참조는 LUIS에서 제공하는 의도 및 엔터티의 미리 작성된 컬렉션에 해당하는 [미리 작성된 도메인](luis-how-to-use-prebuilt-domains.md)에 대한 정보를 제공합니다.

반대로, [사용자 지정 도메인](luis-how-to-start-new-app.md)은 의도 및 모델 없이 시작합니다. 사용자 지정 모델에 미리 작성된 도메인 의도 및 엔터티를 추가할 수 있습니다.

# <a name="supported-domains-across-cultures"></a>문화권에서 지원 되는 도메인

만 지원 되는 문화권은 영어입니다. 

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

|엔터티 형식|description|
|--|--|
|달력|달력 개인 회 및 약속 하는 방법에 대 한 부분이 _되지_ public 이벤트 (world cup 일정, 시애틀 이벤트 달력 등 일반 달력 (어떤 day는 현재와 같은 새로운 않습니다 fall 시작 노동 일 경우).|
|통신|를 호출할 수 있도록 요청 찾기 및 연락처 및 다양 한 다른 통신 관련 요청 (일반적으로 보내는) 추가 텍스트 또는 인스턴트 메시지를 보냅니다. 연락처 이름 쿼리만 통신 도메인에 속하지 않습니다.|
|Email|전자 메일에는 통신 도메인의 하위 도메인입니다. 주로 전자 메일 메시지 보내기 및 받기에 대 한 요청을 포함 합니다.|
|HomeAutomation|HomeAutomation 도메인 의도 및 홈 스마트 장치를 제어 하기 위한 관련 엔터티를 제공 합니다. 주로 광원 및 공조와 관련 된 제어 명령을 지원 하지만 다른 electric 어플라이언스에 대 한 일부 일반화 기능입니다.|
|메모|참고 도메인 의도 및 메모를 작성 하 고 사용자에 대 한 항목 기록에 대 한 엔터티를 제공 합니다.|
|장소|위치는 회사, 기관, 식당, 공용 공간 및 주소를 포함 합니다. 도메인 위치를 찾고 공공 장소 위치, 운영 시간 및 거리 등의 정보에 대 한 요청을 지원 합니다.|
|RestaurantReservation|레스토랑 예약 도메인 식당에 대 한 예약을 처리 하는 것에 대 한 의도 지원 합니다.|
|ToDo|ToDo 도메인 형식의 추가, 표시 및 해당 할 일 항목을 삭제 하는 사용자에 대 한 작업 목록 제공 합니다.|
|공공 시설|유틸리티 도메인 간의 모든 LUIS 일반 도메인은 공통 의도 및 차이 시나리오에서 길이 발언을 포함 하는 미리 작성 된 모델입니다.|
|Weather|날씨 도메인 날씨 조건 및 위치와 시간을 사용 하 여 공지를 확인 또는 날씨 조건에 따라 시간 검사에 중점을 둡니다.|
|웹|웹 도메인 웹 사이트에 대 한 검색에 대 한 의도 및 엔터티를 제공 합니다.|
