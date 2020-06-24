---
title: '방법: 사용자 지정 명령에 확인 추가'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령에서 명령에 대 한 확인을 구현 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85308086"
---
# <a name="add-interaction-rules"></a>상호 작용 규칙 추가

이 문서에서는 **상호 작용 규칙**에 대해 알아봅니다. 구체적이 고 복잡 한 상황을 처리 하는 추가 규칙입니다. 사용자 고유의 사용자 지정 상호 작용 규칙을 자유롭게 작성할 수 있지만이 문서에서는 다음과 같은 대상 시나리오에 대 한 상호 작용 규칙을 사용 합니다.

* 명령 확인
* 명령에 1 단계 수정 추가

상호 작용 규칙에 대 한 자세한 내용을 보려면 [참조](./custom-commands-references.md) 섹션으로 이동 하세요.

## <a name="prerequisites"></a>필수 조건

다음 문서의 단계를 완료 해야 합니다.
> [!div class="checklist"]
> * [방법: 간단한 명령을 사용 하 여 응용 프로그램 만들기](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [방법: 명령에 매개 변수 추가](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>명령에 확인 추가

확인을 추가 하려면 **SetTemperature** 명령을 사용 합니다. 확인을 수행 하기 위해 다음 단계를 사용 하 여 상호 작용 규칙을 만듭니다.

1. 왼쪽 창에서 **SetTemperature** 명령을 선택 합니다.
2. 가운데 창에서 **추가** 를 선택한 다음 **상호 작용 규칙**  >  **확인 명령**을 선택 하 여 상호 작용 규칙을 추가 합니다.

    이렇게 하면 3 개의 상호 작용 규칙이 추가 됩니다 .이 규칙은 사용자에 게 알람의 날짜 및 시간을 확인 하 고 다음 턴에 확인 (예/아니요)을 예상 하도록 요청 합니다.

    1. 다음 구성에 따라 명령 상호 작용 **확인** 규칙을 수정 합니다.
        1. **이름** 이름을으로 바꿉니다 **`Confirm Temperature`** .
        1. **온도 > 필수 매개 변수로** 새 조건 추가
        1. **음성 응답을 보낼 > `Are you sure you want to set the temperature as {Temperature} degrees?` ** 새 작업을 추가 합니다 >
        1. 기대 섹션에서 **사용자의 확인 필요** 를 기본값으로 그대로 둡니다.
      
         > [!div class="mx-imgBorder"]
         > ![필수 매개 변수 응답 만들기](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. **확인 성공** 상호 작용 규칙을 수정 하 여 성공적인 확인 (사용자가 예)을 처리 합니다.
      
          1. **이름** 을로 수정 **`Confirmation temperature succeeded`** 합니다.
          1. 이미 존재 하는 **확인이 성공** 된 상태를 유지 합니다.
          1. **온도 > 필수 매개 변수 >** 새 조건을 형식으로 추가 합니다.
          1. **실행 후 상태의** 기본값은 **완료 규칙 실행**으로 그대로 둡니다.

    1. 확인이 거부 된 경우 시나리오를 처리 하기 위해 **거부 된 확인** (사용자가 언급 하지 않음)을 수정 합니다.

          1. **이름** 을로 수정 **`Confirmation temperature denied`** 합니다.
          1. 이미 존재 하는 **확인이 거부** 된 상태를 유지 합니다.
          1. **온도 > 필수 매개 변수 >** 새 조건을 형식으로 추가 합니다.
          1. **음성 응답을 보낼 > `No problem. What temperature then?` ** 새 작업을 추가 합니다 >
          1. **실행 후 상태의** 기본값은 **사용자 입력 대기**로 그대로 둡니다.

> [!IMPORTANT]
> 이 문서에서는 기본 제공 확인 기능을 사용 했습니다. 또는 상호 작용 규칙을 하나씩 추가 하 여 직접 수행할 수도 있습니다.
   

### <a name="try-out-the-changes"></a>변경 내용 시도

학습 **을 선택 하**고, 학습을 완료 하 고, **테스트**를 선택 합니다.

- Input: 온도를 80도로 설정 합니다.
- 출력: 정상 80?
- 입력: 아니요
- 출력: 문제가 없습니다. 온도는 어떻게 되나요?
- 입력: 83도
- 출력: 정상 83?
- 입력: 예
- 출력: 정상, 온도를 83 각도로 설정


## <a name="implementing-corrections-in-a-command"></a>명령에서 수정 구현

이 섹션에서는 1 단계 수정 사항을 구성 합니다 .이 수정 작업은 이미 실행 된 후에 사용 됩니다. 또한 명령이 아직 충족 되지 않은 경우에는 기본적으로 수정이 사용 되는 방법의 예를 볼 수 있습니다. 명령이 완료 되지 않은 경우 수정 사항을 추가 하려면 **AlarmTone**새 매개 변수를 추가 합니다.

왼쪽 창에서 **Setalarm** 명령을 선택 하 고 **AlarmTone**새 매개 변수를 추가 합니다.
        
- **이름의** > `AlarmTone`
- **형식** > 문자열
- **기본값** > `Chimes`
- **구성** > 내부 카탈로그의 미리 정의 된 입력 값을 허용 합니다.
- **미리 정의 된 입력 값**,  >  `Chimes` `Jingle` 및 `Echo` 각각을 미리 정의 된 개별 입력으로 합니다.


그런 다음 DateTime 매개 변수에 대 한 응답을로 업데이트 `Ready to set alarm with tone as {AlarmTone}. For what time?` 합니다. 그런 다음, 다음과 같이 완료 규칙을 수정 합니다.

1. 기존 완료 규칙 **ConfirmationResponse**를 선택 합니다.
1. 오른쪽 패널에서 기존 작업을 마우스로 가리키고 **편집** 단추를 선택 합니다.
1. 음성 응답 업데이트 대상`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>변경 내용 시도

학습 **을 선택**하 고 교육이 완료 될 때까지 기다린 다음 **테스트**를 선택 합니다.
다음 길이 발언를 사용해 보세요.

- 입력: 경보 설정
- 출력: 경보를 Chimes로 설정할 준비가 되었습니다. 시간에 대해
- 입력: 내일 오전 9 시 jingle으로 톤으로 경보를 설정 합니다.
- 출력: 정상, 2020-05-30 09:00:00에 대해 경보를 설정 합니다. 경보 음은 jingle입니다.

> [!IMPORTANT]
> 진행 중인 명령에서 명시적인 구성 없이 (명령이 아직 완료 되지 않은 경우) 경보 톤을 변경 하는 방법을 확인 합니다. **명령이 아직 수행 되지 않은 경우 턴 번호에 관계 없이 모든 명령 매개 변수에 대해 기본적으로 수정이 활성화 됩니다.**

### <a name="correction-when-command-is-completed"></a>명령이 완료 되 면 수정

또한 사용자 지정 명령 플랫폼은 명령이 완료 된 경우에도 1 단계 수정 기능을 제공 합니다. 그러나이 기능은 기본적으로 사용 하도록 설정 되어 있지 않으며 명시적으로 구성 해야 합니다. 다음 단계를 사용 하 여 1 단계 수정을 구성 합니다.

1. **Setalarm** 명령에서 **update previous command** 형식의 상호 작용 규칙을 추가 하 여 이전에 설정한 경보를 업데이트 합니다. **이전 경보를 업데이트**하기 위해 상호 작용 규칙의 기본 **이름을** 바꿉니다.
1. 기본 조건을 그대로 두고 **이전 명령을 그대로 업데이트** 해야 합니다.
1.  **필수 매개 변수 > DateTime > 형식**으로 새 조건을 추가 합니다.
1. 새 작업을 형식으로 추가 **> 음성 응답 > 간단한 편집기를 보냅니다 `Updating previous alarm time to {DateTime}.` >**
1. 실행 후 상태의 기본값은 **명령이 완료**됨으로 그대로 둡니다.

### <a name="try-out-the-changes"></a>변경 내용 시도

학습 **을 선택 하**고, 학습을 완료 하 고, **테스트**를 선택 합니다.

- 입력: 경보 설정
- 출력: 경보를 Chimes로 설정할 준비가 되었습니다. 몇 시?
- 입력: 내일 오전 9 시 jingle으로 톤으로 경보를 설정 합니다.
- 출력: 정상, 2020-05-21 09:00:00에 대해 경보를 설정 합니다. 경보 음은 jingle입니다.
- 입력: 아니요, 오전 8 시
- 출력: 이전 경보 시간을 2020-05-29 08:00으로 업데이트 합니다.

> [!NOTE]
> 실제 응용 프로그램에서는이 수정 규칙의 작업 섹션에서 작업을 클라이언트에 다시 보내거나 HTTP 끝점을 호출 하 여 시스템의 경보 시간을 업데이트 해야 합니다. 이 작업은 알람 시간을 업데이트 하는 것이 개별,이 경우에는 명령의 다른 특성 (이 경우에는 알람 톤)만을 담당 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방법: 음성 응답을 위한 언어 생성 템플릿 추가](./how-to-custom-commands-add-language-generation-templates.md)