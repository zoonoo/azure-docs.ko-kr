---
title: 미리 빌드된 엔터티
titleSuffix: Azure Cognitive Services
description: LUIS에는 날짜, 시간, 숫자, 측정값 및 통화 등, 일반적인 정보 유형을 인식하기 위한 미리 빌드된 엔터티 집합이 포함되어 있습니다. 미리 빌드된 엔터티 지원은 LUIS 앱의 문화권에 따라 다릅니다.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: ade09c7527e62112ac7225df21e25d4139d99e58
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879000"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>일반 데이터 형식을 인식하는 미리 빌드된 엔터티

LUIS에는 날짜, 시간, 숫자, 측정값 및 통화 등, 일반적인 정보 유형을 인식하기 위한 미리 빌드된 엔터티 집합이 포함되어 있습니다. 

## <a name="add-a-prebuilt-entity"></a>미리 빌드된 엔터티 추가

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 왼쪽에서 **엔터티**를 클릭합니다. 

1. **엔터티** 페이지에서 **미리 빌드된 엔터티 추가**를 클릭합니다.

1. **미리 빌드된 엔터티 추가** 대화 상자에서 미리 빌드된 엔터티 datetimeV2를 선택합니다. 

    ![미리 빌드된 엔터티 추가 대화 상자](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. **완료**를 선택합니다.

## <a name="publish-the-app"></a>앱 게시

미리 빌드된 엔터티의 값을 보는 가장 쉬운 방법은 게시된 엔드포인트에서 쿼리하는 것입니다. 

1. 위쪽 도구 모음에서 **게시**를 선택합니다. **프로덕션**에 게시합니다. 

1. 녹색 성공 알림이 표시되면 **엔드포인트 목록 참조** 링크를 선택하여 엔드포인트를 봅니다.

1. 엔드포인트를 선택합니다. 해당 엔드포인트에 대한 새 브라우저 탭이 열립니다. 브라우저 탭을 연 상태로 **테스트** 섹션을 계속 진행합니다.

## <a name="test"></a>테스트
엔터티를 추가한 후에는 앱을 학습할 필요가 없습니다. 

**q** 매개 변수의 값을 추가하여 엔드포인트에서 새 의도를 테스트하세요. 다음은 **q**에 권장되는 발언을 알려주는 표입니다.

|발언 테스트|엔터티 값|
|--|:--|
|내일 항공편 예약|2018-10-19|
|3월 3일 약속 취소|발언에서 연도가 지정되지 않았기 때문에 LUIS는 가장 가까운 과거의 3월 3일(2018-03-03) 및 미래의 3월 3일(2019-03-03)을 반환합니다.|
|오전 10시에 회의 예약|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>미리 빌드된 엔터티 토큰이 포함된 엔터티 표시
 모델에 [미리 빌드된 번호](luis-reference-prebuilt-number.md)가 추가된 사용자 지정 엔터티 _및_로 표시하려는 텍스트(예: `HH-1234`)가 있는 경우, LUIS 포털에서 사용자 지정 엔터티를 표시할 수 없습니다. 이것은 API로 표시할 수 있습니다. 

 이 유형의 토큰을 표시하려면 미리 빌드된 엔터티로 이미 표시되어 있는 부분에서 LUIS 앱으로부터 미리 빌드된 엔터티를 제거합니다. 앱을 학습할 필요가 없습니다. 그런 다음, 사용자 고유의 사용자 지정 엔터티를 사용하여 토큰을 표시합니다. 그리고 나서 미리 빌드된 엔터티를 LUIS 앱에 다시 추가합니다.

 다른 예를 들면, 발언을 클래스 기본 설정 목록으로 간주하는 것입니다. `I want first year spanish, second year calculus, and fourth year english lit.` LUIS 앱에 사전 빌드된 서수가 추가된 경우 `first`, `second` 및 `fourth`는 이미 서수로 표시됩니다. 서수 및 클래스를 캡처하려는 경우 복합 엔터티를 작성하고 클래스 이름에 대한 사용자 지정 엔터티 및 미리 빌드된 서수를 래핑하면 됩니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)
