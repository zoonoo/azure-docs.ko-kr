---
title: '방법: 음성 응답에 언어 생성 템플릿 사용-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령으로 언어 생성 템플릿을 사용 하는 방법에 대해 알아봅니다. 언어 생성 템플릿을 사용 하 여 클라이언트로 전송 되는 응답을 사용자 지정 하 고 응답의 분산을 도입할 수 있습니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 0cbc57922b31f1b3879bb2cad8a988a1ba4cc368
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85308084"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>음성 응답을 위한 언어 생성 템플릿 추가

이 문서에서는 사용자 지정 명령으로 언어 생성 템플릿을 사용 하는 방법에 대해 알아봅니다. 언어 생성 템플릿을 사용 하 여 클라이언트로 전송 되는 응답을 사용자 지정 하 고 응답의 분산을 도입할 수 있습니다. 언어 생성 사용자 지정은 다음을 통해 구현할 수 있습니다.

- 언어 생성 템플릿 사용
- 적응 식 사용

## <a name="prerequisites"></a>사전 요구 사항

다음 문서의 단계를 완료 해야 합니다.

> [!div class="checklist"]
> * [방법: 간단한 명령을 사용 하 여 응용 프로그램 만들기](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [방법: 명령에 매개 변수 추가](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>언어 생성 템플릿 개요

사용자 지정 명령 템플릿은 BotFramework의 [LG 템플릿을](https://aka.ms/speech/cc-lg-format)기반으로 합니다. 사용자 지정 명령은 필요할 때 새 LG 템플릿을 만들므로 (즉, 매개 변수 또는 작업에서 음성 응답의 경우) LG 템플릿의 이름을 지정할 필요가 없습니다. 따라서 템플릿을 다음과 같이 정의 하는 대신

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

다음과 같이 이름 없이 템플릿의 본문을 정의 하기만 하면 됩니다.

> [!div class="mx-imgBorder"]
> ![템플릿 편집기 예제](./media/custom-commands/template-editor-example.png)


이러한 변경으로 인해 클라이언트에 전송 되는 음성 응답의 변형이 도입 됩니다. 따라서 동일한 utterance에 대해 해당 음성 응답은 제공 된 옵션 중에서 임의로 선택 됩니다.

LG 템플릿을 활용 하면 적응 식을 사용 하 여 명령에 대 한 복잡 한 음성 응답을 정의할 수도 있습니다. 자세한 내용은 [LG 템플릿 형식을](https://aka.ms/speech/cc-lg-format) 참조할 수 있습니다. 기본적으로 사용자 지정 명령은 다음과 같은 사소한 차이점이 있는 모든 기능을 지원 합니다.

* LG 템플릿 엔터티는 $ {entityName}로 표시 됩니다. 사용자 지정 명령에서 엔터티를 사용 하지 않지만 매개 변수를 다음 표현 $ {parameterName} 또는 {parameterName} 중 하나로 사용할 수 있습니다.
* 사용자 지정 명령에서는 템플릿 컴퍼지션 및 확장이 지원 되지 않습니다. 이는 파일을 직접 편집 하 `.lg` 는 것이 아니라 자동으로 만들어진 템플릿의 응답만 있기 때문입니다.
* LG에 의해 삽입 된 사용자 지정 함수는 사용자 지정 명령에서 지원 되지 않습니다. 미리 정의 된 함수는 계속 지원 됩니다.
* 옵션 (strict, replaceNull & lineBreakStyle)은 사용자 지정 명령에서 지원 되지 않습니다.

## <a name="add-template-responses-to-turnonoff-command"></a>TurnOnOff 명령에 템플릿 응답 추가

다음 구성을 사용 하 여 새 매개 변수를 추가 하도록 새 매개 **변수를 추가 하려면이 명령을 수정** 합니다.

| 설정            | 제안 값       | 
| ------------------ | --------------------- | 
| 속성               | `SubjectContext`         | 
| 전역          | unchecked             | 
| 필수           | unchecked               | 
| Type               | String                |
| 기본값      | `all` |
| Configuration      | 내부 카탈로그에서 미리 정의 된 입력 값 허용 | 
| 미리 정의 된 입력 값 | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>완료 규칙 수정

기존 완료 규칙 **ConfirmationResponse**의 **작업** 섹션을 편집 합니다. **작업 편집** 팝업에서 **템플릿 편집기** 로 전환 하 고 텍스트를 다음 예제로 바꿉니다.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

다음과 같이 응용 프로그램을 **학습** 하 고 **테스트** 합니다. 템플릿 값의 여러 대안을 사용 하 고 적응 식도 사용 하 여 응답 변형을 확인 합니다.

* 입력: tv 켜기
* 출력: 양호, tv 켜기
* 입력: tv 켜기
* 출력: 완료, tv 설정
* Input: 조명을 끕니다.
* 출력: 정상, 모든 조명 해제
* 입력: 대화방 광원 끄기
* 출력: 정상, 실내 조명 해제

## <a name="use-custom-voice"></a>사용자 지정 음성 사용

사용자 지정 명령 응답을 사용자 지정 하는 또 다른 방법은 사용자 지정 출력 음성을 선택 하는 것입니다. 기본 음성을 사용자 지정 음성으로 전환 하려면 다음 단계를 사용 합니다.

1. 사용자 지정 명령 응용 프로그램의 왼쪽 창에서 **설정** 을 선택 합니다.
1. 가운데 창에서 **사용자 지정 음성** 을 선택 합니다.
1. 테이블에서 원하는 사용자 지정 또는 공개 음성을 선택 합니다.
1. **저장**을 선택합니다.

> [!div class="mx-imgBorder"]
> ![매개 변수가 있는 샘플 문장](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - **공용 음성**의 경우 **신경망** 은 특정 지역 에서만 사용할 수 있습니다. 가용성을 확인 하려면 [지역/엔드포인트 별 표준 및 신경망](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)을 참조 하세요.
> - **사용자 지정**음성의 경우 사용자 지정 음성 프로젝트 페이지에서 만들 수 있습니다. [사용자 지정 음성 시작을](./how-to-custom-voice.md)참조 하세요.

이제 응용 프로그램은 기본 음성 대신 선택한 음성으로 응답 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 SDK를 사용 하 여 사용자 지정 명령을 통합](./how-to-custom-commands-setup-speech-sdk.md)합니다.