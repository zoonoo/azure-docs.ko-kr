---
title: 사용자 지정 명령 응용 프로그램을 실행 하는 경우 디버그 오류
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램에서 런타임 오류를 디버깅 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 49d9b91df896646da7bf36e077d9f3c9187137dd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021799"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>사용자 지정 명령 응용 프로그램을 실행 하는 경우 디버그 오류

이 문서에서는 사용자 지정 명령 응용 프로그램을 실행 하는 동안 오류가 표시 될 때 디버그 하는 방법을 설명 합니다. 

## <a name="connection-failed"></a>연결 실패

[클라이언트 응용 프로그램 (음성 SDK 사용)](./how-to-custom-commands-setup-speech-sdk.md) 또는 [Windows 음성 도우미 클라이언트](./how-to-custom-commands-developer-flow-test.md)에서 사용자 지정 명령 실행 응용 프로그램을 실행 하는 경우 아래 나열 된 연결 오류가 발생할 수 있습니다.

| 오류 코드 | 세부 정보 |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: 인증 오류가 발생 하 여 WebSocket을 업그레이드 하지 못했습니다. |
| [1002](#error-1002)] | 상태 코드 ' 101 '이 필요한 경우 서버에서 상태 코드 ' 404 '을 반환 했습니다. |

### <a name="error-401"></a>오류 401
- 클라이언트 응용 프로그램에 지정 된 지역이 사용자 지정 명령 응용 프로그램의 지역과 일치 하지 않습니다.

- 음성 리소스 키가 잘못 되었습니다.
    
    음성 리소스 키가 올바른지 확인 합니다.

### <a name="error-1002"></a>오류 1002 
- 사용자 지정 명령 응용 프로그램이 게시 되지 않았습니다.
    
    포털에서 응용 프로그램을 게시 합니다.

- 사용자 지정 명령 applicationId가 잘못 되었습니다.

    사용자 지정 명령 응용 프로그램 ID가 올바른지 확인 합니다.
 음성 리소스 외부의 사용자 지정 명령 응용 프로그램

    사용자 지정 명령 응용 프로그램이 음성 리소스 아래에 만들어졌는지 확인 합니다.

연결 문제를 해결 하는 방법에 대 한 자세한 내용은 [Windows 음성 도우미 클라이언트 문제 해결](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting) 을 참조 하세요.


## <a name="dialog-is-canceled"></a>대화 상자가 취소 됨

사용자 지정 명령 응용 프로그램을 실행 하는 경우 일부 오류가 발생 하면 대화 상자가 취소 됩니다.

- 포털에서 응용 프로그램을 테스트 하는 경우 취소 설명을 직접 표시 하 고 오류 메시지를 발생 시킬 수 있습니다. 

- [Windows 음성 도우미 클라이언트](./how-to-custom-commands-developer-flow-test.md)를 사용 하 여 응용 프로그램을 실행 하는 경우 오류 메시지가 표시 됩니다. **활동 로그** 에서 **CancelledDialog 이벤트** 를 찾을 수 있습니다.

- 클라이언트 응용 프로그램 예제 [클라이언트 응용 프로그램 (음성 SDK 사용)](./how-to-custom-commands-setup-speech-sdk.md)을 수행 하는 경우 오류 메시지가 표시 됩니다. **상태** 에서 **CancelledDialog 이벤트** 를 찾을 수 있습니다.

- 사용자 고유의 클라이언트 응용 프로그램을 빌드하는 경우 언제 든 지 CancelledDialog 이벤트를 처리 하도록 원하는 논리를 디자인할 수 있습니다.

CancelledDialog 이벤트는 다음에 나열 된 취소 코드 및 설명으로 구성 됩니다.

| 취소 코드 | 취소 설명 |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | 허용 된 최대 수를 허용 하 고 나면 진행률이 생성 되지 않습니다. |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | 인식기 사용 할당량 초과 |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | 인식기에 연결 하지 못했습니다. |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | 현재 구독으로는이 응용 프로그램에 액세스할 수 없습니다. |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | 입력이 인식기에 대해 지원 되는 최대 길이를 초과 합니다. |
| [RecognizerNotFound](#recognizer-not-found) | 인식기를 찾을 수 없음 |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | 인식기에 대 한 잘못 된 쿼리 |
| [RecognizerError](#recognizer-return-an-error) | 인식기에서 오류 반환 |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>허용 된 최대 수를 허용 하 고 나면 진행률이 생성 되지 않습니다.
필요한 슬롯이 특정 횟수 만큼 성공적으로 업데이트 되지 않으면 대화가 취소 됩니다. 빌드 최대 수는 3입니다.

### <a name="recognizer-usage-quota-exceeded"></a>인식기 사용 할당량 초과
LUIS (Language Understanding)에는 리소스 사용에 대 한 제한이 있습니다. 일반적으로 "인식기 사용 할당량을 초과 했습니다." 오류는 다음과 같은 경우에 발생할 수 있습니다. 
- LUIS 작성이 제한을 초과 합니다.

    사용자 지정 명령 응용 프로그램에 예측 리소스를 추가 합니다. 
    1. **설정**, LUIS 리소스로 이동 합니다.
    1. **예측 리소스** 에서 예측 리소스를 선택 하거나 **새 리소스 만들기** 를 클릭 합니다. 

- LUIS 예측 리소스가 제한을 초과 합니다.

    F0 예측 리소스를 사용할 경우 10 ~ 10/월 5 개의 쿼리/초 제한이 있습니다.

LUIS 리소스 제한에 대 한 자세한 내용은 [Language Understanding 리소스 사용 및 제한](../luis/luis-limits.md#resource-usage-and-limits) 을 참조 하세요.

### <a name="connection-to-the-recognizer-failed"></a>인식기에 연결 하지 못했습니다.
일반적으로 Language Understanding (LUIS) 인식기에 대 한 일시적인 연결 오류를 의미 합니다. 다시 시도 하 여 문제를 해결 해야 합니다.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>현재 구독으로는이 응용 프로그램에 액세스할 수 없습니다.
구독에 LUIS 응용 프로그램에 액세스할 수 있는 권한이 없습니다. 

### <a name="input-exceeds-the-maximum-supported-length"></a>입력이 지원 되는 최대 길이를 초과 합니다.
입력이 500 자를 초과 했습니다. 입력 utterance 최대 500 자만 허용 합니다.

### <a name="invalid-query-for-the-recognizer"></a>인식기에 대 한 잘못 된 쿼리
입력이 500 자를 초과 했습니다. 입력 utterance 최대 500 자만 허용 합니다.

### <a name="recognizer-return-an-error"></a>인식기에서 오류 반환
LUIS 인식기에서 입력을 인식 하는 동안 오류가 반환 되었습니다.

### <a name="recognizer-not-found"></a>인식기를 찾을 수 없음
사용자 지정 명령 대화 상자 모델에 지정 된 인식기 유형을 찾을 수 없습니다. 현재는 [Language Understanding (LUIS) 인식기](https://www.luis.ai/)만 지원 합니다.

## <a name="other-common-errors"></a>기타 일반적인 오류
### <a name="unexpected-response"></a>예기치 않은 응답
예기치 않은 응답으로 인해 여러 가지 문제가 발생할 수 있습니다. 다음으로 시작 하는 몇 가지 검사:
- 예/예 문장에서 의도 하지 않음

    현재는 with 확인 기능을 사용 하는 경우를 제외 하 고 예/아니요를 지원 하지 않습니다. 예제 문장에 정의 된 모든 Yes/No 의도가 검색 되지 않습니다.

- 명령 간의 유사한 의도 및 예제 문장

    두 명령이 유사한 의도와 예 문장을 공유 하는 경우 LUIS 인식 정확도가 영향을 받을 수 있습니다. 명령 기능 및 예제 문장을 가능한 한 고유 하 게 만들 수 있습니다.

    인식 정확도를 향상 시키는 모범 사례는 [LUIS 모범 사례](../luis/luis-concept-best-practices.md)를 참조 하세요.

- 대화 상자가 취소 됨
    
    위의 섹션에서 취소의 이유를 확인 합니다.

### <a name="error-while-rendering-the-template"></a>템플릿을 렌더링 하는 동안 오류가 발생 했습니다.
정의 되지 않은 매개 변수는 음성 응답에 사용 됩니다. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.
**클라이언트에 작업 보내기** 작업에 정의 된 JSON 페이로드에 빈 매개 변수가 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 샘플을 참조 하세요.](https://aka.ms/speech/cc-samples)