---
title: 새 앱 만들기-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 웹 페이지에서 애플리케이션을 만들고 관리합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 304e76a2bf8bae3e6ee2120b892ef97d89c3decc
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144390"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS 포털에서 새 LUIS 앱 만들기
LUIS 앱을 만드는 몇 가지 방법이 있습니다. LUIS 포털에서 또는 LUIS 제작 [API](developer-reference-resource.md)를 통해 LUIS 앱을 만들 수 있습니다.

## <a name="using-the-luis-portal"></a>LUIS 포털 사용

여러 가지 방법으로 포털에서 새 앱을 만들 수 있습니다.

* 빈 앱으로 시작하고 의도, 발화 및 엔터티를 만듭니다.
* 빈 앱으로 시작하고 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 추가합니다.
* `.lu` `.json` 의도, 길이 발언 및 엔터티가 이미 포함 된 또는 파일에서 LUIS 앱을 가져옵니다.

## <a name="using-the-authoring-apis"></a>작성 API 사용
여러 가지 방법으로 작성 API를 사용하여 새 앱을 만들 수 있습니다.

* [응용 프로그램 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) -빈 앱으로 시작 하 고 의도, 길이 발언 및 엔터티를 만듭니다.
* [미리 빌드된 응용 프로그램 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) -의도, 길이 발언 및 엔터티를 포함 하 여 미리 작성 된 도메인으로 시작 합니다.


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>LUIS에서 새 앱 만들기

1. **내 앱** 페이지에서 **구독**을 선택 하 고 **리소스를 작성** 한 후 **+ 만들기**를 선택 합니다. 

> [!div class="mx-imgBorder"]
> ![LUIS 앱 목록](./media/create-app-in-portal.png)

1. 대화 상자에서 응용 프로그램의 이름 (예:)을 입력 합니다 `Pizza Tutorial` .

    ![새 앱 만들기 대화 상자](./media/create-pizza-tutorial-app-in-portal.png)

1. 응용 프로그램 문화권을 선택한 다음 **완료**를 선택 합니다. 이 시점에서 설명 및 예측 리소스는 선택 사항입니다. 언제 든 지 포털의 **관리** 섹션에서 설정할 수 있습니다.

    > [!NOTE]
    > 애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다.

    앱을 만든 후 LUIS 포털 **에는** 의도 목록이 이미 생성 된 의도 목록이 표시 됩니다 `None` . 이제 빈 앱이 있습니다.

    > [!div class="mx-imgBorder"]
    > ![예 길이 발언 없이 만든 의도가 없는 의도 목록입니다.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>내 앱 페이지에서 사용할 수 있는 기타 작업

상황에 맞는 도구 모음은 다른 작업을 제공 합니다.

* 앱 이름 바꾸기
* 또는를 사용 하 여 파일에서 가져오기 `.lu``.json`
* 앱을 `.lu` ( [ludown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json` 또는 `.zip` ( [LUIS 컨테이너](luis-container-howto.md)의 경우)로 내보내기
* 컨테이너 끝점 로그를 가져와 길이 발언 끝점을 검토 합니다.
* 오프 라인 분석을 위해 끝점 로그를로 내보내기 `.csv`
* 앱 삭제

## <a name="next-steps"></a>다음 단계

앱 디자인에 의도 검색을 포함 하는 경우 [새 의도를 만들고](luis-how-to-add-intents.md)길이 발언 예제를 추가 합니다. 앱 디자인이 데이터 추출 인 경우 예를 길이 발언에 추가 하 고, [엔터티를 만들고](luis-how-to-add-example-utterances.md), 예제 길이 발언에 해당 엔터티로 레이블을 추가 합니다.
