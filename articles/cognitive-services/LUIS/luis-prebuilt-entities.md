---
title: LUIS(Language Understanding)용으로 미리 빌드된 엔터티
titleSuffix: Azure Cognitive Services
description: LUIS에는 날짜, 시간, 숫자, 측정값 및 통화 등, 일반적인 정보 유형을 인식하기 위한 미리 빌드된 엔터티 집합이 포함되어 있습니다. 미리 빌드된 엔터티 지원은 LUIS 앱의 문화권에 따라 다릅니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647598"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>일반 데이터 형식을 인식하는 미리 빌드된 엔터티

LUIS에는 날짜, 시간, 숫자, 측정값 및 통화 등, 일반적인 정보 유형을 인식하기 위한 미리 빌드된 엔터티 집합이 포함되어 있습니다. 

## <a name="add-a-prebuilt-entity"></a>미리 빌드된 엔터티 추가

1. **내 앱** 페이지에서 해당 이름을 클릭하여 앱을 열고 왼쪽에서 **엔터티**를 클릭합니다. 

1. **엔터티** 페이지에서 **미리 빌드된 엔터티 관리**를 클릭합니다.

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


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)
