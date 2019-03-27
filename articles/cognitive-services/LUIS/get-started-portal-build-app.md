---
title: '빠른 시작: LUIS 포털에서 새 앱 만들기'
titleSuffix: Language Understanding - Azure Cognitive Services
description: 이 빠른 시작에서는 LUIS 포털에서 새 앱을 만듭니다. 앱, 의도 및 엔터티의 기본 부분을 만든 다음, 대화형 테스트 패널에 샘플 사용자 발화를 제공하여 예측된 의도를 가져옵니다. 앱 빌드는 무료로, Azure 구독이 필요하지 않습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783260"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>빠른 시작: LUIS 포털에서 새 앱 만들기

이 빠른 시작에서는 [LUIS 포털](https://www.luis.ai)에서 새 앱을 만듭니다. 앱, **의도** 및 **엔터티**의 기본 부분을 만든 다음, 대화형 테스트 패널에 샘플 사용자 발화를 제공하여 예측된 의도를 가져옵니다.

앱 빌드는 무료로, Azure 구독이 필요하지 않습니다. 앱을 배포할 준비가 되면 Azure Cognitive Service 리소스를 만들고 앱에 할당합니다. 이 배포 프로세스는 [다음 빠른 시작](get-started-portal-deploy-app.md)에 나옵니다.

## <a name="create-app"></a>앱 만들기 

1. 브라우저에서 [LUIS 포털](https://www.luis.ai)을 열고 로그인합니다. 이번이 처음 로그인하는 경우이면 무료 LUIS 포털 사용자 계정을 만들어야 합니다.

1. 상황에 맞는 도구 모음에서 **새 앱 만들기**를 선택합니다.

    [![LUIS 포털에서 새 앱 만들기](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. 팝업 창에서 다음 설정을 사용하여 앱을 구성하고 **완료**를 선택합니다.

    |설정 이름| 값 | 목적|
    |--|--|--|
    |Name|`myEnglishApp`|고유한 LUIS 앱 이름<br>필수|
    |문화권|**English**|사용자의 발화 언어, **en-us**<br>필수|
    |설명|`App made with LUIS Portal`|앱에 대한 설명<br>선택 사항|

    ![새 앱 설정 입력](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>의도 만들기 

이 앱을 만든 이후의 단계는 의도를 만드는 것입니다. 의도는 사용자의 텍스트를 분류하는 방법입니다. 두 함수, 즉 사람들이 직업을 찾아 지원하도록 도와주는 함수와 직업 지원 양식을 찾는 함수를 제공하는 인적 자원 앱이 있는 경우 이러한 두 가지 다른 _의도_를 다음 의도에 맞춥니다.

|의도|사용자의 예제 텍스트<br>(_발화_)|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. 앱을 만들면 **빌드** 섹션의 **의도** 페이지가 표시됩니다. **새 의도 만들기**를 선택합니다. 

    [![새 의도 만들기 단추 선택](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. 의도 이름 `FindForm`을 입력하고 **완료**를 선택합니다.

    ![FindForm의 의도 이름 입력](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>발화 예제 추가 

의도를 만든 후 다음 단계는 예제 발화를 추가하는 것입니다. 사용자 텍스트의 의도를 LUIS 의도에 매핑하는 채팅 봇 또는 다른 클라이언트 애플리케이션에서 사용자가 입력하는 텍스트입니다. 

이 예제 애플리케이션의 `FindForm` 의도에서, 예제 발화에는 양식 번호가 포함됩니다. 이 번호는 클라이언트 애플리케이션에서 사용자 요청을 이행하는 데 필요한 발화 내의 중요 정보입니다. 

다음 15개의 예제 발화를 `FindForm` 의도에 추가하세요. 

|#|예제 발화|
|--|--|
|1|Looking for hrf-123456|
|2|Where is the human resources form hrf-234591?|
|3|hrf-345623, where is it|
|4|Is it possible to send me hrf-345794|
|5|Do I need hrf-234695 to apply for an internal job?|
|6|Does my manager need to know I'm applying for a job with hrf-234091|
|7|Where do I send hrf-234918? Do I get an email response it was received?|
|8|hrf-234555|
|9|When was hrf-234987 updated?|
|10|Do I use form hrf-876345 to apply for engineering positions|
|11|Was a new version of hrf-765234 submitted for my open req?|
|12|Do I use hrf-234234 for international jobs?|
|13|hrf-234598 spelling mistake|
|14|will hrf-234567 be edited for new requirements|
|15|hrf-123456, hrf-123123, hrf-234567|

이러한 예제 발화의 다음과 같은 측면은 용도에 따라 다릅니다.

* 발화 길이
* 문장 부호
* 단어 선택
* 동사 시제(is, was, will be)
* 단어 순서

[![FindForm 의도의 예제 발화 입력](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>정규식 엔터티 만들기 

런타임 예측 응답의 일부로 반환되는 양식 번호를 가져오려면 양식을 엔터티로 표시해야 합니다. 양식 번호 텍스트는 고도로 구조화되어 있으므로 정규식 엔터티를 사용하여 표시할 수 있습니다. 다음 단계를 사용하여 엔터티를 만듭니다. 

1. 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다. 

1. 엔터티 페이지에서 **새 엔터티 만들기**를 선택합니다.

1. 이름 `Human Resources Form Number`를 입력하고 **Regex** 엔터티 형식을 선택한 후 정규식 `hrf-[0-9]{6}`을 입력합니다. 그러면 리터럴 문자, `hrf-`가 일치하는 문자로 검색되고 정확히 6자리가 허용됩니다. 

    ![정규식 엔터티에 대한 엔터티 정보를 입력합니다.](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. **완료**를 선택합니다. 

## <a name="add-example-utterances-to-none-intent"></a>None 의도에 예제 발화 추가

**None** 의도는 대체 의도로, 비워 둘 수 없습니다. 이 의도의 경우 이 앱의 나머지 의도 10개마다 1개의 발화가 있어야 합니다. 

**None** 의도의 예제 발화는 클라이언트 애플리케이션 영역 외부에 있어야 합니다. 

1. 왼쪽 메뉴에서 **의도**를 선택한 다음, 의도 목록에서 **None**을 선택합니다.

1. 다음 예제 발화를 의도에 추가하세요.

    |None 의도 예제 발화|
    |--|
    |개가 짖어서 시끄럽다|
    |피자 주문해 줘|
    |바다의 펭귄|

    이 인적 자원 앱에서 이러한 예제 발화는 해당 영역 외부에 있습니다. 인적 자원 영역에 동물, 식품 또는 해양이 포함되는 경우 이러한 예제 발화를 **None** 의도에 사용하지 않아야 합니다. 

## <a name="train-the-app"></a>앱 학습

오른쪽 탐색 영역에서 **학습**을 선택하여 의도 및 엔터티 모델 변경 내용을 앱의 현재 버전에 적용합니다. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>예제 발화의 정규식 엔터티 확인

1. 왼쪽 메뉴에서 **의도**를 선택하여 엔터티가 **FindForm** 의도에 있는지 확인하고 **FindForm** 의도를 선택합니다. 

    예제 발화에서 해당 엔터티가 있는 위치가 표시됩니다. 엔터티 이름 대신 원래 텍스트를 보고 싶으면 도구 모음에서 **엔터티 보기**를 전환합니다.

    [![엔터티로 표시된 모든 예제 발화](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>대화형 테스트 창을 사용하여 새 앱 테스트

LUIS 포털에서 대화형 **테스트** 창을 사용하여 앱에 아직 표시되지 않은 새 발화에서 엔터티가 추출되었는지 검증합니다.

1. 오른쪽 상단 메뉴에서 **테스트**를 선택합니다.

1. 새 발화를 추가한 후 Enter 키를 누릅니다.

    ```Is there a form named hrf-234098```

    ![테스트 창에서 새 발화 테스트](./media/get-started-portal-build-app/test-new-utterance.png)

    상단 예측 의도가 90% 이상의 신뢰도(0.977)를 갖는 **FindForm**이고, 값이 hrf-234098인 **인적 자원 양식 번호** 엔터티가 추출됩니다. 

## <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작을 완료했을 때 다음 빠른 시작으로 이동되지 않으면 위쪽 탐색 메뉴에서 **내 앱**을 선택합니다. 그런 다음, 목록에서 앱의 왼쪽 확인란을 선택하고 목록 위의 상황에 맞는 도구 모음에서 **삭제**를 선택합니다. 

[![내 앱 목록에서 앱 삭제](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [2. 앱 배포](get-started-portal-deploy-app.md)