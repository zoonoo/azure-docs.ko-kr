---
title: 새 앱 만들기
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS(Language Understanding) 웹 페이지에서 애플리케이션을 만들고 관리합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: ddfee80c67c22c7c6016ed87dc17925c91637d21
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714001"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS 포털에서 새 LUIS 앱 만들기
LUIS 앱을 만드는 몇 가지 방법이 있습니다. [LUIS](https://www.luis.ai) 포털에서 또는 LUIS 제작 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)를 통해 LUIS 앱을 만들 수 있습니다.

## <a name="using-the-luis-portal"></a>LUIS 포털 사용
여러 가지 방법으로 LUIS 포털에서 새 앱을 만들 수 있습니다.

* 빈 앱으로 시작하고 의도, 발화 및 엔터티를 만듭니다.
* 빈 앱으로 시작하고 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 추가합니다.
* 이미 의도, 발언 및 엔터티가 포함된 JSON 파일에서 LUIS 앱을 가져옵니다.

## <a name="using-the-authoring-apis"></a>작성 API 사용
여러 가지 방법으로 작성 API를 사용하여 새 앱을 만들 수 있습니다.

* 빈 앱으로 [시작](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)하고 의도, 발화 및 엔터티를 만듭니다.
* 미리 빌드된 도메인으로 [시작](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5)합니다.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>LUIS에서 새 앱 만들기

1. **내 앱** 페이지에서 **새 앱 만들기**를 선택합니다.

    ![LUIS 앱 목록](./media/luis-create-new-app/apps-list.png)


2. 대화 상자에서 애플리케이션 이름을 “TravelAgent”로 지정합니다.

    ![새 앱 만들기 대화 상자](./media/luis-create-new-app/create-app.png)

3. 애플리케이션 문화권을 선택한 다음(TravelAgent App의 경우 영어 선택), **완료**를 선택합니다. 

    > [!NOTE]
    > 애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다. 


## <a name="next-steps"></a>다음 단계

앱의 첫 번째 작업은 [의도를 추가](luis-how-to-add-intents.md)하는 것입니다.