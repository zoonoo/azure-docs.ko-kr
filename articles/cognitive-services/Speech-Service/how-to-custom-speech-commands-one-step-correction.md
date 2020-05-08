---
title: '방법: 사용자 지정 명령에 1 단계 수정 추가 (미리 보기)-음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령의 명령에 대해 1 단계 수정을 구현 하는 방법에 대해 설명 합니다.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858252"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>방법: 사용자 지정 명령에 1 단계 수정 추가 (미리 보기)

이 문서에서는 명령에 1 단계 확인을 추가 하는 방법에 대해 알아봅니다.

1 단계 수정은 방금 완료 된 명령을 업데이트 하는 데 사용 됩니다. 즉, 경보를 설정 하는 경우 주의를 변경 하 고 경보 시간을 업데이트할 수 있습니다. 이러한 경우의 예는 다음과 같습니다.

- 입력: 내일 정오에 경보 설정
- 출력: 정상, 2020-05-02 12:00:00에 대해 설정 되는 경보
- 입력: 아니요, 내일 오후 1 시
- 출력: 정상

일반적으로 명령 완료의 결과로 클라이언트에서 작업을 실행 하는 실제 시나리오-이 문서에서는 백 엔드 응용 프로그램에서 경보를 업데이트 하는 메커니즘이 개발자에 게 있다고 가정 합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 문서의 단계를 완료 해야 합니다.
> [!div class="checklist"]

> * [빠른 시작: 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-new.md)
> * [빠른 시작: 매개 변수를 사용 하 여 사용자 지정 명령 만들기 (미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
> * [방법: 사용자 지정 명령에 확인 추가 (미리 보기)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>1 단계 수정에 대 한 상호 작용 규칙 추가 

1 단계 수정을 시연 하기 위해 [방법: 사용자 지정 명령에 확인 추가 (미리 보기)](./how-to-custom-speech-commands-confirmations.md)에서 만든 **setalarm** 명령을 확장 하겠습니다.
1. 이전에 설정한 경보를 업데이트 하는 상호 작용 규칙을 추가 합니다. 

    이 규칙은 사용자에 게 알람의 날짜 및 시간을 확인 하 고 다음 턴에 확인 (예/아니요)을 예상 하도록 요청 합니다.

   | 설정               | 제안 값                                                  | 설명                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 규칙 이름             | 이전 경보 업데이트                                            | 규칙의 용도를 설명 하는 이름입니다.          |
   | 조건            | 이전 명령은 필수 매개 변수 > DateTime & 업데이트 해야 합니다.                | 규칙을 실행할 수 있는 시기를 결정 하는 조건    |   
   | 동작               | 음성 응답 보내기-> 단순 편집기-이전 경보를 {DateTime} (으)로 업데이트 >      | 규칙 조건이 참인 경우 수행할 동작입니다. |
   | 실행 후 상태 | 명령이 완료 됨        | 턴 후의 사용자 상태                   |

1. 방금 만든 규칙을 상호 작용 규칙의 맨 위로 이동 합니다 (패널에서 규칙을 선택 하 고 가운데 창 위쪽의 아이콘 아래 `...` 에 있는 위쪽 화살표 클릭).
   > [!div class="mx-imgBorder"]
   > ![범위 유효성 검사 추가](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > 실제 응용 프로그램에서이 규칙의 작업 섹션에서는 클라이언트에 작업을 다시 보내거나 HTTP 끝점을 호출 하 여 시스템의 경보를 업데이트 합니다.

## <a name="try-it-out"></a>기능 직접 사용해 보기

을 `Train`선택 하 고, 교육이 완료 될 때 `Test`까지 기다린 후를 선택 합니다.

- 입력: 내일 정오에 경보 설정
- 출력: 2020-05-02 12:00:00에 대 한 경보를 설정 하 시겠습니까?
- 입력: 예
- 출력: 정상, 2020-05-02 12:00:00에 대해 설정 되는 경보
- 입력: 아니요, 내일 2pm
- 출력: 이전 경보를 2020-05-02 14:00:00으로 업데이트
