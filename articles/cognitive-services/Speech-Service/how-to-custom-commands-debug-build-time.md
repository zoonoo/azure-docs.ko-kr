---
title: 사용자 지정 명령 응용 프로그램을 제작할 때의 디버그 오류 (미리 보기)
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 명령 응용 프로그램을 작성할 때 오류를 디버깅 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 9c84b35318637f5b89e6c88c0ebb3fd6616533fc
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023128"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>사용자 지정 명령 응용 프로그램을 제작할 때 디버그 오류

이 문서에서는 사용자 지정 명령 응용 프로그램을 빌드하는 동안 오류가 표시 될 때 디버그 하는 방법을 설명 합니다. 

## <a name="errors-when-creating-an-application"></a>응용 프로그램을 만들 때 발생 하는 오류
사용자 지정 명령은 사용자 지정 명령 응용 프로그램을 만들 때 [LUIS](https://www.luis.ai/) 에서도 응용 프로그램을 만듭니다. 

[LUIS는 제작 리소스 당 500 응용 프로그램을 제한](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits)합니다. 이미 500 응용 프로그램이 있는 제작 리소스를 사용 하는 경우 LUIS 응용 프로그램 만들기가 실패할 수 있습니다. 

선택한 LUIS authoring 리소스의 응용 프로그램이 500 개 미만인 지 확인 합니다. 그렇지 않은 경우 새 LUIS authoring 리소스를 만들거나 다른 리소스를 전환 하거나 LUIS 응용 프로그램을 정리할 수 있습니다.  

## <a name="errors-when-deleting-an-application"></a>응용 프로그램을 삭제할 때 발생 하는 오류
### <a name="cant-delete-luis-application"></a>LUIS 응용 프로그램을 삭제할 수 없습니다.
사용자 지정 명령 응용 프로그램을 삭제 하는 경우 사용자 지정 명령은 사용자 지정 명령 응용 프로그램과 연결 된 LUIS 응용 프로그램을 삭제 하려고 할 수도 있습니다.

LUIS 응용 프로그램을 삭제 하는 데 실패 한 경우 [LUIS](https://www.luis.ai/) 계정으로 이동 하 여 수동으로 삭제 합니다.

### <a name="toomanyrequests"></a>TooManyRequests
한 번에 많은 수의 응용 프로그램을 삭제 하려고 하면 ' TooManyRequests ' 오류가 표시 될 가능성이 높습니다. 이러한 오류는 Azure에서 삭제 요청을 제한 하는 것을 의미 합니다. 

페이지를 새로 고치고 덜 응용 프로그램을 삭제 해 보세요.

## <a name="errors-when-modifying-an-application"></a>응용 프로그램을 수정할 때 발생 하는 오류

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>매개 변수나 웹 끝점을 삭제할 수 없습니다.
매개 변수가 사용 되는 경우에는 해당 매개 변수를 삭제할 수 없습니다. 음성 응답, 샘플 문장, 조건, 작업에서 매개 변수의 참조를 모두 제거 하 고 다시 시도 하세요.

### <a name="cant-delete-a-web-endpoint"></a>웹 끝점을 삭제할 수 없습니다.
사용 중인 웹 끝점은 삭제할 수 없습니다. 웹 끝점을 제거 하기 전에이 웹 끝점을 사용 하는 모든 **호출 웹 끝점** 작업을 제거 합니다.

## <a name="errors-when-training-an-application"></a>응용 프로그램을 학습할 때 발생 하는 오류
### <a name="built-in-intents"></a>기본 제공 의도
LUIS에는 기본 제공 예/아니요가 있습니다. "예", "아니요"만 포함 하는 샘플 문장이 있으면 학습에 실패 합니다. 

| 키워드 | 변형 | 
| ------- | --------- | 
| 예 | 확인 합니다. |
| 아니요 | 맞습니다 | 

### <a name="common-sample-sentences"></a>일반적인 샘플 문장
사용자 지정 명령은 여러 명령 간에 공유 되는 일반적인 샘플 문장을 허용 하지 않습니다. 한 명령의 일부 샘플 문장이 다른 명령에 이미 정의 되어 있으면 응용 프로그램의 교육이 실패할 수 있습니다. 

여러 명령 간에 공통적인 샘플 문장이 공유 되어 있지 않은지 확인 합니다. 

여러 명령에서 샘플 문장을 분산 하는 모범 사례는 [LUIS 모범 사례](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)를 참조 하세요.

### <a name="empty-sample-sentences"></a>빈 샘플 문장
각 명령에 대 한 샘플 문장이 하나 이상 있어야 합니다.

### <a name="undefined-parameter-in-sample-sentences"></a>샘플 문장의 정의 되지 않은 매개 변수
하나 이상의 매개 변수가 샘플 문장에서 사용 되었지만 정의 되지 않았습니다.

### <a name="training-takes-too-long"></a>학습 시간이 너무 오래 걸립니다.
LUIS 교육은 더 작은 예제로 신속 하 게 학습할 수 있습니다. 너무 많은 예제 문장을 추가 하지 마세요. 

비슷한 많은 예제 문장이 있는 경우 매개 변수를 정의 하 고 패턴으로 추상화 한 다음 예제 문장에 추가 합니다.

예를 들어 아래 예제 문장에 대 한 매개 변수 {차량}을 정의 하 고 예제 문장에 "책 a {차량}"만 추가할 수 있습니다.

| 예제 문장 | 무늬 | 
| ------- | ------- | 
| 차량 책 | 책 a {차량} | 
| 항공권 예약 | 책 a {차량} |
| 책 taxi | 책 a {차량} |

LUIS 교육의 모범 사례는 [LUIS 모범 사례](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)를 참조 하세요.

## <a name="cant-update-luis-key"></a>LUIS 키를 업데이트할 수 없습니다.
### <a name="reassign-to-e0-authoring-resource"></a>E0 제작 리소스에 다시 할당
LUIS는 LUIS 응용 프로그램을 E0 authoring resource에 재할당 하는 기능을 지원 하지 않습니다.

제작 리소스를 F0에서 E0로 변경 하거나 다른 E0 리소스로 변경 해야 하는 경우 응용 프로그램을 다시 만듭니다. 

기존 응용 프로그램을 빠르게 내보내고 새 응용 프로그램으로 가져오려면 [Azure DevOps를 사용 하 여 연속 배포](./how-to-custom-commands-deploy-cicd.md)를 참조 하세요.

### <a name="save-button-is-disabled"></a>저장 단추를 사용할 수 없습니다.
응용 프로그램에 LUIS 예측 리소스를 할당 하지 않은 경우에는 예측 리소스를 추가 하지 않고 제작 리소스를 변경 하려고 할 때 저장 단추를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub의 샘플을 참조 하세요.](https://aka.ms/speech/cc-samples)
