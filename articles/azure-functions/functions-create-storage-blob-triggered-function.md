---
title: "Azure에서 Blob Storage에 의해 트리거되는 함수 만들기 | Microsoft Docs"
description: "Azure Functions를 사용하여 Azure Blob Storage에 추가된 항목에 의해 호출되는 서버를 사용하지 않는 함수를 만듭니다."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b00f9e7491048a5dd60e0decab1727a1ff01448e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Azure Blob Storage에 의해 트리거되는 함수 만들기

파일이 업로드되거나 Azure Blob Storage에서 업데이트될 때 트리거되는 함수를 만드는 방법을 알아봅니다.  

![로그에서 메시지 보기.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

이 토픽의 모든 단계를 완료하는 데 5분 이상 걸리지 않습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[Microsoft Azure Storage 탐색기](http://storageexplorer.com/)를 다운로드하고 설치해야 합니다. 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Blob Storage 트리거 함수 만들기

함수 앱을 확장하고 **함수** 옆에 있는 **+** 단추를 클릭하고, 원하는 언어의 **BlobTrigger** 템플릿을 클릭합니다. 그런 다음 표에 지정된 것처럼 **만들기**를 클릭합니다.

![Blob Storage 트리거 함수 만들기.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
    
| 설정      |  제안 값   | 설명                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **Path**   | mycontainer/{name}    | 모니터링되는 Blob Storage의 위치입니다. 바인딩에 _name_ 매개 변수로 전달되는 Blob의 파일 이름입니다.  |
| **저장소 계정 연결** | AzureWebJobStorage | 함수 앱에 이미 사용된 저장소 계정 연결을 사용하거나 새로 만들 수 있습니다.  |
| **함수 이름 지정** | 함수 앱에서 고유 | 큐 트리거 함수의 이름입니다. | 

다음으로 Azure Storage 계정에 연결하고 **mycontainer** 컨테이너를 만듭니다.

## <a name="create-the-container"></a>컨테이너 만들기

1. 함수에서 **통합**을 클릭하고 **설명서**를 확장하여 **계정 이름** 및 **계정 키**를 모두 복사합니다. 이러한 자격 증명을 사용하여 저장소 계정에 연결합니다. 저장소 계정에 이미 연결된 경우 4단계로 건너뜁니다.
 
    ![저장소 계정 연결 자격 증명 가져오기.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

2. [Microsoft Azure Storage 탐색기](http://storageexplorer.com/) 도구를 실행하고 왼쪽의 연결 아이콘을 클릭하고 **저장소 계정 이름 및 키 사용**을 선택하고 **다음**을 클릭합니다.

    ![저장소 계정 탐색기 도구 실행.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)
    
3. 1단계에서 **계정 이름** 및 **계정 키**를 입력하고 **다음**을 클릭한 후 **연결**을 클릭합니다. 
  
    ![저장소 자격 증명 입력 및 연결.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

4. 연결된 저장소 계정을 확장하고 **Blob 컨테이너**를 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 클릭하고 `mycontainer`를 입력한 후 Enter 키를 누릅니다.
 
    ![저장소 큐 만들기.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

이제 Blob 컨테이너가 있고 컨테이너에 파일을 업로드하여 함수를 테스트할 수 있습니다.  

## <a name="test-the-function"></a>함수 테스트

1. Azure Portal로 돌아가서 함수를 찾은 후 페이지 맨 아래에 있는 **로그**를 확장하고 로그 스트리밍이 일시 중지되지 않았는지 확인합니다.

2. Storage 탐색기에서 저장소 계정, **Blob 컨테이너** 및 **mycontainer**를 확장합니다. **업로드**를 클릭한 후 **파일 업로드...**를 클릭합니다.

    ![Blob 컨테이너에 파일 업로드.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

2. **파일 업로드** 대화 상자에서 **파일** 필드를 클릭합니다. 로컬 컴퓨터에서 이미지 파일과 같은 파일을 찾아 선택하고 **열기** 및 **업로드**를 차례로 클릭합니다.   
 
3. 함수 로그로 돌아가 Blob를 읽었는지 확인합니다. 

   ![로그에서 메시지 보기.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > 함수 앱이 기본 소비 계획에서 실행될 때 추가 또는 업데이트되는 Blob과 트리거되는 함수 사이에 최대 몇 분의 지연이 있을 수 있습니다. Blob 트리거 함수에서 대기 시간을 줄여야 하는 경우 App Service 계획에서 함수 앱을 실행하는 것이 좋습니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

Blob Storage에서 Blob이 추가 또는 업데이트될 때 실행되는 함수를 만들었습니다. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Blob Storage 트리거에 대한 자세한 내용은 [Azure Functions Blob Storage 바인딩](functions-bindings-storage-blob.md)을 참조하세요. 




