---
title: Azure에서 Blob 스토리지에 의해 트리거되는 함수 만들기
description: Azure Functions를 사용하여 Blob 스토리지 컨테이너에 추가된 항목에 의해 호출되는 서버를 사용하지 않는 함수를 만듭니다.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123125"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Blob 스토리지에 의해 트리거되는 Azure의 함수 만들기

파일이 업로드되거나 Blob 스토리지 컨테이너에서 업데이트될 때 트리거되는 함수를 만드는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

+ Azure 구독 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

## <a name="create-an-azure-function-app"></a>Azure 함수 앱 만들기

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

새 함수 앱을 만들었습니다.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="함수 앱을 성공적으로 만들었습니다." border="true":::

다음으로 새 함수 앱에서 함수를 만듭니다.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Azure Blob 스토리지 트리거 함수 만들기

1. **함수**를 선택한 다음, **+ 추가**를 선택하여 새 함수를 추가합니다.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Azure Portal에서 함수 템플릿을 선택합니다." border="true":::

1. **Azure Blob 스토리지 트리거** 템플릿을 선택합니다.

1. 이미지 아래의 표에 지정된 설정을 사용합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Blob 스토리지 트리거 함수에 이름을 지정하고 구성합니다." border="true":::

    | 설정 | 제안 값 | Description |
    |---|---|---|
    | **새 함수** | 함수 앱에서 고유 | 이 Blob 트리거 함수의 이름입니다. |
    | **Path**   | samples-workitems/{name}    | 모니터링되는 Blob Storage의 위치입니다. 바인딩에 _name_ 매개 변수로 전달되는 Blob의 파일 이름입니다.  |
    | **Storage 계정 연결** | AzureWebJobsStorage | 함수 앱에 이미 사용된 스토리지 계정 연결을 사용하거나 새로 만들 수 있습니다.  |

1. **함수 만들기**를 선택하여 함수를 만듭니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Blob 스토리지 트리거 함수를 만듭니다." border="true":::

다음으로 **samples-workitems** 컨테이너를 만듭니다.

## <a name="create-the-container"></a>컨테이너 만들기

1. 함수의 **개요** 페이지에서 리소스 그룹을 선택합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Azure Portal 리소스 그룹을 선택합니다." border="true":::

1. 리소스 그룹의 스토리지 계정을 찾아 선택합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="스토리지 계정에 액세스합니다." border="true":::

1. **컨테이너**를 선택한 다음, **+ 컨테이너**를 선택합니다. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Azure Portal의 스토리지 계정에 컨테이너를 추가합니다." border="true":::

1. **이름** 필드에 `samples-workitems`를 입력한 다음, **만들기**를 선택합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="스토리지 컨테이너에 이름을 지정합니다." border="true":::

이제 Blob 컨테이너가 있고 컨테이너에 파일을 업로드하여 함수를 테스트할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트

1. Azure Portal로 돌아가서 함수를 찾은 후 페이지 맨 아래에 있는 **로그**를 확장하고 로그 스트리밍이 일시 중지되지 않았는지 확인합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Azure Portal에서 로그를 확장합니다." border="true":::

1. 별도의 브라우저 창에서 Azure Portal의 리소스 그룹으로 이동하고 스토리지 계정을 선택합니다.

1. **컨테이너**를 선택한 다음,**samples-workitems** 컨테이너를 선택합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Azure Portal의 samples-workitems 컨테이너로 이동합니다." border="true":::

1. **업로드**를 선택하고 폴더 아이콘을 선택하여 업로드할 파일을 선택합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Blob 컨테이너에 파일을 업로드합니다." border="true":::

1. 로컬 컴퓨터에서 이미지 파일과 같은 파일을 찾아 선택합니다. **열기**를 선택한 다음, **업로드**를 선택합니다.

1. 함수 로그로 돌아가 Blob을 읽었는지 확인합니다.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="로그에서 메시지를 봅니다." border="true":::

    >[!NOTE]
    > 함수 앱이 기본 소비 계획에서 실행될 때 추가 또는 업데이트되는 Blob과 트리거되는 함수 사이에 최대 몇 분의 지연이 있을 수 있습니다. Blob 트리거 함수에서 대기 시간을 줄여야 하는 경우 App Service 계획에서 함수 앱을 실행하는 것이 좋습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

Blob Storage에서 Blob이 추가 또는 업데이트될 때 실행되는 함수를 만들었습니다. Blob Storage 트리거에 대한 자세한 내용은 [Azure Functions Blob Storage 바인딩](functions-bindings-storage-blob.md)을 참조하세요.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
