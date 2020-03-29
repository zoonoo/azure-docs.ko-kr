---
title: '빠른 시작: 매개 변수(미리 보기)를 사용하여 사용자 지정 명령 만들기 - 음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램에 매개 변수를 추가합니다.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348524"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>빠른 시작: 매개 변수를 사용하여 사용자 지정 명령 만들기(미리 보기)

이전 [문서에서는](./quickstart-custom-speech-commands-create-new.md)매개 변수 없이 명령에 응답 하는 새 사용자 지정 명령 프로젝트를 만들었습니다.

이 문서에서는 여러 장치를 켜고 끄는 것을 처리 할 수 있도록 매개 변수로이 응용 프로그램을 확장합니다.

## <a name="create-parameters"></a>Create Parameters

1. [이전에 만든](./quickstart-custom-speech-commands-create-new.md) 프로젝트 열기
1. 이제 명령이 켜고 끌 수 있으므로 명령의 이름을 "TurnOnOff"로 바꿉니다.
   - 명령 의 이름 위에 마우스를 가져 가서 편집 아이콘을 선택하여 이름을 변경합니다.
1. 사용자가 장치를 켜거나 끌지 여부를 나타내는 새 매개 변수 만들기
   - 매개변수 `+` 섹션 옆의 아이콘을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![매개 변수 만들기](media/custom-speech-commands/create-on-off-parameter.png)

   | 설정            | 제안 값     | 설명                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | 이름               | 온오프 (것)               | 매개 변수에 대한 설명 이름                                                                     |
   | 글로벌          | unchecked           | 이 매개 변수에 대한 값이 프로젝트의 모든 명령에 전역적으로 적용되는지 여부를 나타내는 확인란 |
   | 필수           | checked             | 명령을 완료하기 전에 이 매개 변수의 값이 필요한지 여부를 나타내는 확인란          |
   | 응답 템플릿  | "- 켜기 또는 끄기?"      | 알 수 없는 경우 이 매개 변수의 값을 묻는 프롬프트                                       |
   | Type               | String              | 숫자, 문자열 또는 날짜 시간과 같은 매개 변수 유형                                               |
   | Configuration      | 문자열 목록         | 문자열의 경우 문자열 목록은 입력을 가능한 값 집합으로 제한합니다.                                      |
   | 문자열 목록 값 | on, off             | 문자열 목록 매개 변수의 경우 가능한 값 집합과 해당 동의어                                |

   - 다음으로 아이콘을 `+` 다시 선택하여 장치 이름을 나타내는 두 번째 매개 변수를 추가합니다. 이 예에서는 TV와 팬

   | 설정            | 제안 값       | 설명                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | 이름               | 제목 장치         | 매개 변수에 대한 설명 이름                                                                     |
   | 글로벌          | unchecked             | 이 매개 변수에 대한 값이 프로젝트의 모든 명령에 전역적으로 적용되는지 여부를 나타내는 확인란 |
   | 필수           | checked               | 명령을 완료하기 전에 이 매개 변수의 값이 필요한지 여부를 나타내는 확인란          |
   | 응답 템플릿  | "- 어떤 장치?"     | 알 수 없는 경우 이 매개 변수의 값을 묻는 프롬프트                                       |
   | Type               | String                | 숫자, 문자열 또는 날짜 시간과 같은 매개 변수 유형                                               |
   | Configuration      | 문자열 목록           | 문자열의 경우 문자열 목록은 입력을 가능한 값 집합으로 제한합니다.                                      |
   | 문자열 목록 값 | TV, 팬               | 문자열 목록 매개 변수의 경우 가능한 값 집합과 해당 동의어                                |
   | 동의어 (tv)      | 텔레비전, 텔레비전     | 문자열 목록 매개 변수의 가능한 각 값에 대한 선택적 동의어                                      |

## <a name="add-sample-sentences"></a>샘플 문장 추가

매개 변수를 사용하면 가능한 모든 조합을 포함하는 샘플 문장을 추가하는 것이 좋습니다. 예를 들어:

1. 전체 매개 변수 정보 -`"turn {OnOff} the {SubjectDevice}"`
1. 부분 매개 변수 정보 -`"turn it {OnOff}"`
1. 매개 변수 정보 없음 -`"turn something"`

서로 다른 양의 정보가 있는 샘플 문장을 사용하면 사용자 지정 명령 응용 프로그램에서 부분 정보로 원샷 해상도와 다중 회전 해상도를 모두 해결할 수 있습니다.

이를 염두에 두고 샘플 문장을 편집하여 아래에 제시된 매개 변수를 사용합니다.

> [!TIP]
> 샘플 문장 편집기에서 매개 변수를 참조하는 곱슬 중괄호를 사용합니다. - `turn {OnOff} the {SubjectDevice}`탭 완성을 사용하여 이전에 만든 매개 변수를 참조합니다.

> [!div class="mx-imgBorder"]
> ![매개 변수가 있는 샘플 문장](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>완료 규칙에 매개 변수 추가

[이전 빠른 시작에서](./quickstart-custom-speech-commands-create-new.md)만든 완료 규칙을 수정합니다.

1. 새 조건을 추가하고 필수 매개변수를 선택합니다. 둘 `OnOff` 다 선택 및`SubjectDevice`
1. 사용할 `OnOff` 음성 응답 작업 편집 `SubjectDevice`및 다음

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>체험

테스트 채팅 패널을 열고 몇 가지 상호 작용을 시도합니다.

- 입력: TV 끄기
- 출력 : 좋아, TV를 끄기

- 입력: 텔레비전 끄기
- 출력 : 좋아, TV를 끄기

- 입력: 끄기
- 출력: 어떤 장치?
- 입력: TV
- 출력 : 좋아, TV를 끄기

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: 사용자 지정 음성(미리 보기)으로 사용자 지정 명령 사용](./quickstart-custom-speech-commands-select-custom-voice.md)
