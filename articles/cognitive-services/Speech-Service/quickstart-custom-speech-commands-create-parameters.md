---
title: '빠른 시작: 매개 변수 (미리 보기)-음성 서비스를 사용 하 여 사용자 지정 명령 만들기'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램에 매개 변수를 추가 합니다.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: d1c709beb225dff0ae616a23eb67eb57f4643ac9
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812754"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)

[이전 문서](./quickstart-custom-speech-commands-create-new.md)에서는 매개 변수 없이 명령에 응답 하는 새 사용자 지정 명령 프로젝트를 만들었습니다.

이 문서에서는 여러 장치를 켜고 끄는 기능을 처리할 수 있도록 매개 변수를 사용 하 여이 응용 프로그램을 확장 합니다.

## <a name="create-parameters"></a>Create Parameters

1. [이전에 만든](./quickstart-custom-speech-commands-create-new.md) 프로젝트 열기
1. 이제 명령이 on 및 off로 처리 되기 때문에 명령의 이름을 "이 아닌"로 바꿉니다.
   - 명령 이름 위로 마우스를 이동 하 고 편집 아이콘을 선택 하 여 이름을 변경 합니다.
1. 사용자가 장치를 켤 지 여부를 나타내는 새 매개 변수를 만듭니다.
   - 매개 변수 섹션 옆에 있는 `+` 아이콘을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > 매개 변수](media/custom-speech-commands/create-on-off-parameter.png) ![만듭니다.

   | 설정            | 제안 값 | 설명                                                                                               |
   | ------------------ | --------------- | --------------------------------------------------------------------------------------------------------- |
   | name               | 마이크           | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                                     |
   | 전역          | 선택되지 않음       | 이 매개 변수의 값이 프로젝트의 모든 명령에 전역적으로 적용 되는지 여부를 나타내는 확인란 |
   | 필수           | checked         | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란          |
   | 응답 템플릿  | 설정 또는 해제 여부      | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다.                                       |
   | Type               | string          | 숫자, 문자열 또는 날짜 시간과 같은 매개 변수의 형식입니다.                                               |
   | 구성      | 문자열 목록     | 문자열의 경우 문자열 목록에서 가능한 값 집합에 대 한 입력을 제한 합니다.                                      |
   | 문자열 목록 값 | on, off         | 문자열 목록 매개 변수에서 사용할 수 있는 값 및 해당 동의어 집합                                |

   - 그런 다음 `+` 아이콘을 다시 선택 하 여 장치의 이름을 나타내는 두 번째 매개 변수를 추가 합니다. 이 예의 경우 tv 및 팬

   | 설정            | 제안 값   | 설명                                                                                               |
   | ------------------ | ----------------- | --------------------------------------------------------------------------------------------------------- |
   | name               | 주제 장치     | 매개 변수에 대 한 설명이 포함 된 이름입니다.                                                                     |
   | 전역          | 선택되지 않음         | 이 매개 변수의 값이 프로젝트의 모든 명령에 전역적으로 적용 되는지 여부를 나타내는 확인란 |
   | 필수           | checked           | 명령을 완료 하기 전에이 매개 변수의 값이 필요한 지 여부를 나타내는 확인란          |
   | 응답 템플릿  | 어떤 장치 입니까?     | 이 매개 변수 값을 알 수 없는 경우 요청 하는 프롬프트입니다.                                       |
   | Type               | string            | 숫자, 문자열 또는 날짜 시간과 같은 매개 변수의 형식입니다.                                               |
   | 구성      | 문자열 목록       | 문자열의 경우 문자열 목록에서 가능한 값 집합에 대 한 입력을 제한 합니다.                                      |
   | 문자열 목록 값 | tv, 팬           | 문자열 목록 매개 변수에서 사용할 수 있는 값 및 해당 동의어 집합                                |
   | 동의어 (tv)      | 텔레비전, 고 대 | 문자열 목록 매개 변수의 가능한 각 값에 대 한 선택적 동의어                                      |

## <a name="add-sample-sentences"></a>샘플 문장 추가

매개 변수를 사용 하 여 가능한 모든 조합을 다루는 샘플 문장을 추가 하는 것이 좋습니다. 다음은 그 예입니다.

1. 전체 매개 변수 정보-`"turn {OnOff} the {SubjectDevice}"`
1. 부분 매개 변수 정보-`"turn it {OnOff}"`
1. 매개 변수 정보 없음-`"turn something"`

사용자 지정 명령 응용 프로그램에서는 정보를 사용 하는 샘플 문장을 통해 단일 샷 해상도와 다중 턴 해상도를 모두 부분 정보로 해결할 수 있습니다.

이 점을 염두에 두면 아래에서 제안 하는 것 처럼 매개 변수를 사용 하도록 샘플 문장을 편집 합니다.

> [!TIP]
> 샘플 문장 편집기에서 중괄호를 사용 하 여 매개 변수를 참조 합니다. - 탭 완성 기능을 사용 하 여 이전에 만든 매개 변수를 참조할 `turn {OnOff} the {SubjectDevice}`.

> [!div class="mx-imgBorder"]
> 매개 변수를 사용 하 여 샘플 문장 ![](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>완료 규칙에 매개 변수 추가

[이전 빠른](./quickstart-custom-speech-commands-create-new.md)시작에서 만든 완료 규칙을 수정 합니다.

1. 새 조건을 추가 하 고 필수 매개 변수를 선택 합니다. `OnOff` 및 `SubjectDevice`를 모두 선택 합니다.
1. 음성 응답 작업을 편집 하 여 `OnOff`를 사용 하 고 `SubjectDevice`합니다.

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>사용해 보기

테스트 채팅 패널을 열고 몇 가지 상호 작용을 시도 합니다.

- 입력: tv 끄기
- 출력: 확인, tv 끄기

- 입력: tv 끄기
- 출력: 확인, tv 끄기

- 입력: 해제
- 출력: 어떤 장치 입니까?
- 입력: tv
- 출력: 확인, tv 끄기

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: 음성 SDK (미리 보기)를 사용 하 여 사용자 지정 명령 응용 프로그램에 연결](./quickstart-custom-speech-commands-speech-sdk.md)

