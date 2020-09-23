---
title: Azure에서 큐 메시지에 의해 트리거되는 함수 만들기
description: Azure Functions를 사용하여 Azure 큐에 제출된 메시지에 의해 호출되는 서버를 사용하지 않는 함수를 만듭니다.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90981573"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Azure Queue Storage에 의해 트리거되는 함수 만들기

Azure Storage 큐에 메시지가 제출될 때 트리거되는 함수를 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

## <a name="create-an-azure-function-app"></a>Azure 함수 앱 만들기

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="함수 앱을 성공적으로 만들었습니다." border="true":::

다음으로 새 함수 앱에서 함수를 만듭니다.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>큐 트리거 함수 만들기

1. **함수**를 선택한 다음, **+ 추가**를 선택하여 새 함수를 추가합니다.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Azure Portal에서 함수 템플릿을 선택합니다." border="true":::

1. **Azure Queue Storage 트리거** 템플릿을 선택합니다.

1. 이미지 아래의 표에 지정된 설정을 사용합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Queue Storage 트리거 함수에 이름을 지정하고 구성합니다." border="true":::


    | 설정 | 제안 값 | Description |
    |---|---|---|
    | **이름** | 함수 앱에서 고유 | 큐 트리거 함수의 이름입니다. |
    | **큐 이름**   | myqueue-items    | Storage 계정에서 연결할 큐의 이름입니다. |
    | **Storage 계정 연결** | AzureWebJobsStorage | 함수 앱에 이미 사용된 스토리지 계정 연결을 사용하거나 새로 만들 수 있습니다.  |    

1. **함수 만들기**를 선택하여 함수를 만듭니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Queue Storage 트리거 함수를 만듭니다." border="true":::

다음으로 Azure Storage 계정에 연결하고 **myqueue-items** 스토리지 큐를 만듭니다.

## <a name="create-the-queue"></a>큐 만들기

1. 함수의 **개요** 페이지에서 리소스 그룹을 선택합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Azure Portal 리소스 그룹을 선택합니다." border="true":::

1. 리소스 그룹의 스토리지 계정을 찾아 선택합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="스토리지 계정에 액세스합니다." border="true":::

1. **큐**를 선택하고 **+ 큐**를 선택합니다. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Azure Portal에서 스토리지 계정에 큐를 추가합니다." border="true":::

1. **이름** 필드에 `myqueue-items`를 입력한 다음, **만들기**를 선택합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Queue Storage 컨테이너에 이름을 지정합니다." border="true":::

이제 스토리지 큐가 있고 메시지를 큐에 추가하여 함수를 테스트할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트

1. Azure Portal로 돌아가서 함수를 찾은 후 페이지 맨 아래에 있는 **로그**를 확장하고 로그 스트리밍이 일시 중지되지 않았는지 확인합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Azure Portal에서 로그를 확장합니다." border="true":::

1. 별도의 브라우저 창에서 Azure Portal의 리소스 그룹으로 이동하고 스토리지 계정을 선택합니다.

1. **큐**을 선택하고 **myqueue-items** 컨테이너를 선택합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Azure Portal에서 myqueue-items 큐로 이동합니다." border="true":::

1. **메시지 추가**를 선택하고 "Hello World!"를 **메시지 텍스트**에 입력합니다. **확인**을 선택합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="스크린샷 선택한 메시지 추가 단추와 메시지 텍스트 필드가 강조 표시 됩니다." border="true":::

1. 몇 초 동안 기다린 다음 함수 로그로 돌아가서 새 메시지를 큐에서 읽었는지 확인합니다.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="로그에서 메시지를 봅니다." border="true":::

1. 스토리지 큐로 돌아가 **새로 고침**을 선택하고 메시지가 처리되었는지와 더 이상 큐에 없는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

스토리지 큐에 메시지가 추가될 때 실행되는 함수를 만들었습니다. Queue Storage 트리거에 대한 자세한 내용은 [Azure Functions Storage 큐 바인딩](functions-bindings-storage-queue.md)을 참조하세요.

첫 번째 함수를 만들었으니, 다른 큐에 메시지를 작성하는 출력 바인딩을 함수에 추가해 보겠습니다.

> [!div class="nextstepaction"]
> [Functions를 사용하여 Azure Storage 큐에 메시지 추가](functions-integrate-storage-queue-output-binding.md)
