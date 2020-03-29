---
title: 새 앱 만들기 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 웹 페이지에서 애플리케이션을 만들고 관리합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221482"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS 포털에서 새 LUIS 앱 만들기
LUIS 앱을 만드는 몇 가지 방법이 있습니다. LUIS 포털에서 또는 LUIS 제작 [API](developer-reference-resource.md)를 통해 LUIS 앱을 만들 수 있습니다.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>LUIS 포털 사용

미리 보기 포털에서 다음과 같은 여러 가지 방법으로 새 앱을 만들 수 있습니다.

* 빈 앱으로 시작하고 의도, 발화 및 엔터티를 만듭니다.
* 빈 앱으로 시작하고 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 추가합니다.
* 의도, 발언 및 `.lu` `.json` 엔터티가 이미 포함된 파일에서 LUIS 앱을 가져옵니다.

## <a name="using-the-authoring-apis"></a>작성 API 사용
여러 가지 방법으로 작성 API를 사용하여 새 앱을 만들 수 있습니다.

* [응용 프로그램 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - 빈 앱으로 시작하여 의도, 발언 및 엔터티를 만듭니다.
* [미리 빌드된 응용 프로그램 추가](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - 의도, 발언 및 엔터티를 포함하여 미리 빌드된 도메인으로 시작합니다.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>LUIS에서 새 앱 만들기

1. **내 앱** 페이지에서 구독을 선택하고 리소스를 작성한 다음 **+ 만들기**를 선택합니다. 무료 평가판 키를 사용하는 경우 [작성 리소스를 만드는](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)방법을 알아봅니다.

    ![LUIS 앱 목록](./media/create-app-in-portal.png)


1. 대화 상자에 응용 프로그램 이름(예: `Pizza Tutorial`을 입력합니다.)

    ![새 앱 만들기 대화 상자](./media/create-pizza-tutorial-app-in-portal.png)

1. 응용 프로그램 문화문화를 선택한 다음 **완료**를 선택합니다. 설명 및 예측 리소스는 이 시점에서 선택 사항입니다. 포털의 **관리** 섹션에서 언제든지 설정할 수 있습니다.

    > [!NOTE]
    > 애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다. 

    앱을 만든 후 LUIS 포털에는 **Intents** 이미 만든 `None` 의도가 있는 의도 목록이 표시됩니다. 이제 빈 앱이 있습니다. 
    
    > [!div class="mx-imgBorder"]
    > ![의도 는 예제 발언 없이 만든 없음 의도로 나열됩니다.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>사용 가능한 기타 작업

컨텍스트 도구 모음은 다음과 같은 다른 작업을 제공합니다.

* 앱 이름 바꾸기
* `.lu` 또는`.json`
* (LUDown에 [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)대 한) `.json` `.zip` 또는 [(LUIS 컨테이너에](luis-container-howto.md)대 한) 응용 프로그램을 `.lu` 내보내기
* 컨테이너 끝점 로그를 가져와 끝점 발언을 검토합니다.
* 오프라인 분석을 위한 끝점 `.csv`로그를 로그 로그 로그 로그 로그 로그 로그 로그 로그 로그 로그
* 앱 삭제

## <a name="next-steps"></a>다음 단계

앱 디자인에 의도 검색이 포함된 경우 [새 의도를 만들고](luis-how-to-add-intents.md)예제 발언을 추가합니다. 앱 디자인이 데이터 추출인 경우 None 의도에 예제 발언을 추가한 다음 [엔터티를 만들고](luis-how-to-add-example-utterances.md)예제 발언에 해당 엔터티에 레이블을 지정합니다. 
