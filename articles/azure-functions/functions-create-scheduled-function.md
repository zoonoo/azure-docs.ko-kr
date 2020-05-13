---
title: Azure에서 일정에 따라 실행되는 함수 만들기
description: Azure에서 정의한 일정에 따라 실행되는 함수를 만드는 방법을 알아봅니다.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: be539efdb66b0a9bda583960484f40fae1e18235
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123503"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Azure에서 타이머에 따라 트리거되는 함수 만들기

Azure Functions를 사용하여 정의한 일정에 따라 실행되는 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 함수를 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

+ Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-azure-function-app"></a>Azure 함수 앱 만들기

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

새 함수 앱을 사용할 준비가 되었습니다. 다음으로 새 함수 앱에서 함수를 만듭니다.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="함수 앱을 성공적으로 만들었습니다." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>타이머 트리거 함수 만들기

1. 함수 앱에서 **함수**를 선택 하 고 **+ 추가** 를 선택 합니다. 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Azure Portal에 함수를 추가 합니다." border="true":::

1. **타이머 트리거** 템플릿을 선택 합니다. 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Azure Portal에서 타이머 트리거를 선택 합니다." border="true":::

1. 이미지 아래의 표에 지정 된 설정을 사용 하 여 새 트리거를 구성한 다음 **함수 만들기**를 선택 합니다.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Azure Portal에서 타이머 트리거를 선택 합니다." border="true":::
    
    | 설정 | 제안 값 | 설명 |
    |---|---|---|
    | **이름** | Default | 타이머 트리거 함수의 이름을 정의합니다. |
    | **일정** | 0 \* /1 \* \* \*\* | 1분마다 함수가 실행되도록 예약하는 6개 필드의 [CRON 식](functions-bindings-timer.md#ncrontab-expressions)입니다. |

## <a name="test-the-function"></a>함수 테스트

1. 함수에서 **코드 + 테스트** 를 선택 하 고 로그를 확장 합니다.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Azure Portal에서 타이머 트리거를 테스트 합니다." border="true":::

1. 로그에 기록 된 정보를 확인 하 여 실행을 확인 합니다.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Azure Portal에서 타이머 트리거를 확인 합니다." border="true":::

이제 함수의 일정을 변경하여 1분이 아니라 1시간에 한 번 실행되도록 합니다.

## <a name="update-the-timer-schedule"></a>타이머 일정 업데이트

1. 함수에서 **통합**을 선택 합니다. 여기에서 함수에 대 한 입력 및 출력 바인딩을 정의 하 고 일정도 설정 합니다. 

1. **타이머 (mytimer)** 를 선택 합니다.

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Azure Portal에서 타이머 일정을 업데이트 합니다." border="true":::

1. **일정** 값을로 업데이트 하 `0 0 */1 * * *` 고 **저장**을 선택 합니다.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Azure Portal에서 함수 업데이트 타이머 일정." border="true":::

함수는 이제 한 시간마다 한 번씩 실행됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

일정에 따라 실행 되는 함수를 만들었습니다. 타이머 트리거에 대한 자세한 내용은 [Azure Functions를 사용하여 코드 실행 예약](functions-bindings-timer.md)을 참조하세요.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
