---
title: 새 앱 만들기 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 웹 페이지에서 애플리케이션을 만들고 관리합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.openlocfilehash: 2dd06a7b4c8e6296cda747d17fd3d5be5db0af6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018891"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS 포털에서 새 LUIS 앱 만들기
LUIS 앱을 만드는 몇 가지 방법이 있습니다. LUIS 포털에서 또는 LUIS 제작 [API](developer-reference-resource.md)를 통해 LUIS 앱을 만들 수 있습니다.

## <a name="using-the-luis-portal"></a>LUIS 포털 사용

여러 가지 방법으로 포털에서 새 앱을 만들 수 있습니다.

* 빈 앱으로 시작하고 의도, 발화 및 엔터티를 만듭니다.
* 빈 앱으로 시작하고 [미리 빌드된 도메인](./howto-add-prebuilt-models.md)을 추가합니다.
* 이미 의도, 발화 및 엔터티가 포함된 `.lu` 또는 `.json` 파일에서 LUIS 앱을 가져옵니다.

## <a name="using-the-authoring-apis"></a>작성 API 사용
여러 가지 방법으로 작성 API를 사용하여 새 앱을 만들 수 있습니다.

* [애플리케이션 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - 빈 앱으로 시작하고 의도, 발화 및 엔터티를 만듭니다.
* [미리 빌드된 애플리케이션 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - 의도, 발화 및 엔터티를 포함하여 미리 빌드된 도메인으로 시작합니다.


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>LUIS에서 새 앱 만들기

1. **내 앱** 페이지에서 **구독**, **제작 리소스** 및 **+ 만들기** 를 차례로 선택합니다. 

> [!div class="mx-imgBorder"]
> ![LUIS 앱 목록](./media/create-app-in-portal.png)

1. 대화 상자에서 애플리케이션의 이름을 입력합니다(예: `Pizza Tutorial`).

    ![새 앱 만들기 대화 상자](./media/create-pizza-tutorial-app-in-portal.png)

1. 애플리케이션 문화권을 선택한 다음 **완료** 를 선택합니다. 이때 설명 및 예측 리소스는 선택 사항입니다. 포털의 **관리** 섹션에서 언제든지 설정할 수 있습니다.

    > [!NOTE]
    > 애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다.

    앱을 만든 후 LUIS 포털에는 이미 생성된 `None` 의도와 함께 **의도** 목록이 표시됩니다. 이제 빈 앱이 생겼습니다.

    > [!div class="mx-imgBorder"]
    > ![예제 발화 없이 생성된 None 의도가 포함된 의도 목록](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>내 앱 페이지에서 사용할 수 있는 기타 작업

상황에 맞는 도구 모음에서 다른 작업을 제공합니다.

* 앱 이름 바꾸기
* `.lu` 또는 `.json`을 사용하여 파일에서 가져오기
* 앱을 `.lu`([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)의 경우), `.json` 또는 `.zip`([LUIS 컨테이너](luis-container-howto.md)의 경우)으로 내보내기
* 엔드포인트 발화를 검토할 수 있도록 컨테이너 엔드포인트 로그 가져오기
* 오프라인 분석을 위해 엔드포인트 로그를 `.csv`로 내보내기
* 앱 삭제

## <a name="next-steps"></a>다음 단계

앱 디자인에 의도 검색을 포함하는 경우 [새 의도를 만들고](luis-how-to-add-intents.md) 예제 발화를 추가합니다. 앱 디자인이 데이터 추출 전용인 경우 None 의도에 예제 발화를 추가한 다음 [엔터티를 만들고](./luis-how-to-add-entities.md) 해당 엔터티를 사용하여 예제 발화에 레이블을 지정합니다.