---
title: 새 앱 만들기
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS(Language Understanding) 웹 페이지에서 애플리케이션을 만들고 관리합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9d650a17ddfac6461341e50c4693e4522d9628b3
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148191"
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

## <a name="import-an-app-from-file"></a>파일에서 앱 가져오기

1. **내 앱** 페이지에서 **새 앱 가져오기**를 선택합니다.
1. 팝업 대화 상자에서 유효한 앱 JSON 파일을 선택 하 고 선택한 **수행**합니다.

### <a name="import-errors"></a>가져오기 오류

가능한 오류 다음과 같습니다. 

* 앱 이름이 이미 있습니다. 앱을 다시 가져오기 및 설정 합니다 **선택적 이름** 새 이름입니다. 

## <a name="export-app-for-backup"></a>백업에 대 한 앱 내보내기

1. 온 **Myapps** 페이지에서 **내보내기**합니다.
1. 선택 **JSON으로 내보내기**합니다. 브라우저 앱의 현재 사용 중인 버전을 다운로드합니다.
1. 모델을 보관 하려면 백업 시스템에이 파일을 추가 합니다.

## <a name="export-app-for-containers"></a>컨테이너에 대 한 앱 내보내기

1. 온 **Myapps** 페이지에서 **내보내기**합니다.
1. 선택 **컨테이너로 내보냅니다** 다음 내보내려는 게시 된 슬롯 (프로덕션 또는 단계)를 선택 합니다.
1. 이 파일을 사용 하면 [LUIS 컨테이너](luis-container-howto.md)합니다. 

    제외한에서는 학습 된 내보내기 아직 게시 된 모델 LUIS 컨테이너를 사용 하는 데 관심이 있다면로 이동 합니다 **버전** 여기에서 내보내고 페이지입니다. 

## <a name="delete-app"></a>앱 삭제

1. **내 앱** 페이지에서 앱 행 끝에 있는 세 개의 점(...)을 선택합니다.
1. 메뉴에서 **삭제**를 선택합니다.
1. 확인 창에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

앱의 첫 번째 작업은 [의도를 추가](luis-how-to-add-intents.md)하는 것입니다.
