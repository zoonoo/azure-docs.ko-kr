---
title: '방법: 사용자 지정 명령(미리 보기)에 1단계 수정 추가 - 음성 서비스'
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령에서 명령에 대 한 한 단계 수정을 구현 하는 방법을 설명 합니다.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75462390"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>방법: 사용자 지정 명령에 한 단계 수정 추가(미리 보기)

이 문서에서는 명령에 한 단계 확인을 추가하는 방법을 배웁니다.

1단계 수정은 방금 완료된 명령을 업데이트하는 데 사용됩니다.

즉, 알람을 설정한 경우 마음을 바꾸고 알람 시간을 업데이트할 수 있습니다.

- 입력: 정오에 내일 알람 설정
- 출력: "확인, 알람 설정 12/06/2019 12:00:00"
- 입력: 아니요, 내일 오후 1시
- 출력: "확인

이는 개발자가 백 엔드 응용 프로그램에서 경보를 업데이트하는 메커니즘을 가지고 있음을 의미합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 문서의 단계를 완료해야 합니다.

- [빠른 시작: 사용자 지정 명령 만들기(미리 보기)](./quickstart-custom-speech-commands-create-new.md)
- [빠른 시작: 매개 변수를 사용하여 사용자 지정 명령 만들기(미리 보기)](./quickstart-custom-speech-commands-create-parameters.md)
- [방법: 사용자 지정 명령에 확인 추가(미리 보기)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>1단계 수정을 위한 고급 규칙 추가 

한 단계 수정을 보여 주기 위해 확인 방법 에서 만든 **SetAlarm** 명령을 [확장해](./how-to-custom-speech-commands-confirmations.md)보겠습니다.
 
1. 고급 규칙을 추가하여 이전 경보를 업데이트합니다. 

    이 규칙은 사용자에게 알람의 날짜와 시간을 확인하도록 요청하며 다음 턴에 대한 확인(예/아니요)을 예상하고 있습니다.

   | 설정               | 제안 값                                                  | 설명                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 규칙 이름             | 이전 경보 업데이트                                            | 규칙의 목적을 설명하는 이름          |
   | 조건            | 업데이트LastCommand & 필수 매개 변수 - DateTime                | 규칙을 실행할 수 있는 시기를 결정하는 조건    |   
   | 동작               | 음성 응답 - "- 이전 알람을 {DateTime}로 업데이트"       | 규칙 조건이 true일 때 취할 작업 |
   | 실행 후 상태 | 전체 명령                                                 | 턴 후 사용자의 상태                   |

1. 방금 만든 규칙을 고급 규칙 의 맨 위로 이동합니다(패널의 규칙을 스크롤하여 UP 화살표를 클릭).
   > [!div class="mx-imgBorder"]
   > ![범위 유효성 검사 추가](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> 실제 응용 프로그램에서는 이 규칙의 작업 섹션에서 클라이언트에 활동을 다시 보내거나 HTTP 끝점을 호출하여 시스템의 경보를 업데이트합니다.

## <a name="try-it-out"></a>체험

테스트 패널을 선택하고 몇 가지 상호 작용을 시도합니다.

- 입력: 정오에 내일 알람 설정
- 출력: "2019년 12월 07일 12:00:00에 알람을 설정하시겠습니까?"
- 입력: 예
- 출력: "확인, 알람 설정 12/07/2019 12:00:00"
- 입력: 아니요, 내일 오후 1시
- 출력: "이전 알람을 2019년 12월 07일 13:00:00로 업데이트"
