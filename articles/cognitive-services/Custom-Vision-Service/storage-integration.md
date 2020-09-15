---
title: 알림 및 모델 백업을 위해 Azure storage 통합
titleSuffix: Azure Cognitive Services
description: Custom Vision 모델을 학습 하거나 내보낼 때 Azure storage를 통합 하 여 푸시 알림을 받는 방법에 대해 알아봅니다. 내보낸 모델의 백업을 저장할 수도 있습니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: f4d9cc4c02ab062c73e9dbd977d9ea9e6ccdb60d
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532784"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>알림 및 백업을 위해 Azure storage 통합

Custom Vision 프로젝트를 Azure blob storage 큐와 통합 하 여 프로젝트의 학습/내보내기 작업 및 게시 된 모델의 백업 복사본에 대 한 푸시 알림을 받을 수 있습니다. 이 기능은 긴 작업이 실행 될 때 서비스에서 결과를 지속적으로 폴링하는 것을 방지 하는 데 유용 합니다. 대신 저장소 큐 알림을 워크플로에 통합할 수 있습니다.

이 가이드에서는 이러한 REST Api를 사용 하는 방법을 보여 줍니다. Postman과 같은 HTTP 요청 서비스를 사용 하 여 요청을 실행할 수도 있습니다.

> [!NOTE]
> 푸시 알림은 **Createproject** API의 선택적 _notificationqueueuri_ 매개 변수에 따라 달라 지 며, 모델 백업에도 선택적 _exportModelContainerUri_ 매개 변수를 사용 해야 합니다. 이 가이드에서는 전체 기능 집합에 대해 모두를 사용 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure의 Custom Vision 리소스입니다. 없는 경우 Azure Portal으로 이동 하 여 [새 Custom Vision 리소스를 만듭니다](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). 이 기능은 현재 단일 키로 인식 서비스 리소스를 지원 하지 않습니다.
- Blob 컨테이너를 사용 하는 Azure Storage 계정 이 단계에 대 한 도움이 필요한 경우 [Azure Storage 랩의 연습 1을](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) 따릅니다.

## <a name="set-up-azure-storage-integration"></a>Azure storage 통합 설정

Azure Portal에서 Custom Vision 교육 리소스로 이동 하 고, **id** 페이지를 선택 하 고, 시스템 할당 관리 id를 사용 하도록 설정 합니다.

다음으로 Azure Portal의 저장소 리소스로 이동 합니다. **액세스 제어 (IAM)** 페이지로 이동 하 고 각 통합 기능에 대 한 역할 할당을 추가 합니다.
* 모델 백업 기능을 사용 하려는 경우 Custom Vision 교육 리소스를 선택 하 고 **저장소 Blob 데이터 참가자** 역할을 할당 합니다. 
* 그런 다음 Custom Vision 교육 리소스를 선택 하 고 알림 큐 기능을 사용 하려는 경우 **저장소 큐 데이터 참가자** 를 할당 합니다.

> [!div class="mx-imgBorder"]
> ![저장소 계정 역할 할당 추가 페이지](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>통합 Url 가져오기

다음으로 Custom Vision 리소스가 이러한 끝점에 액세스할 수 있도록 하는 Url을 가져옵니다.

알림 큐 통합 URL에 대해 저장소 계정의 **큐** 페이지로 이동 하 여 새 큐를 추가 하 고 해당 url을 임시 위치에 저장 합니다.

> [!div class="mx-imgBorder"]
> ![Azure storage 큐 페이지](./media/storage-integration/queue-url.png) 

모델 백업 통합 URL에 대해 저장소 계정의 **컨테이너** 페이지로 이동 하 여 새 컨테이너를 만듭니다. 그런 다음이를 선택 하 고 **속성** 페이지로 이동 합니다. URL을 임시 위치에 복사 합니다.
 
> [!div class="mx-imgBorder"]
> ![Azure storage 컨테이너 속성 페이지](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Custom Vision 프로젝트 통합

이제 통합 Url이 있으므로 Azure Storage 기능을 통합 하는 새 Custom Vision 프로젝트를 만들 수 있습니다. 또한 기존 프로젝트를 업데이트 하 여 기능을 추가할 수 있습니다.

### <a name="create-new-project"></a>새 프로젝트 만들기

[Createproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API를 호출 하는 경우 선택적 매개 변수 _ExportModelContainerUri_ 및 _notificationqueueuri_를 추가 합니다. 이전 섹션에서 얻은 URL 값을 할당 합니다. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

응답이 수신 되 면 `200/OK` url이 성공적으로 설정 된 것입니다. JSON 응답에도 URL 값이 표시 됩니다.

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>기존 프로젝트 업데이트

Azure storage 기능 통합을 사용 하 여 기존 프로젝트를 업데이트 하려면 업데이트 하려는 프로젝트의 ID를 사용 하 여 [UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API를 호출 합니다. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

요청 본문 ()을 `body` 다음 JSON 형식으로 설정 하 고 _ExportModelContainerUri_ 및 _notificationqueueuri_에 적절 한 값을 입력 합니다.

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

응답이 수신 되 면 `200/OK` url이 성공적으로 설정 된 것입니다.

## <a name="verify-the-connection"></a>연결 확인 

이전 섹션의 API 호출은 Azure storage 계정에서 새 정보를 이미 트리거한 것입니다. 

지정 된 컨테이너에서 **Customvision-TestPermission** 폴더 안에 테스트 blob이 있어야 합니다. 이 blob은 일시적 으로만 존재 합니다.

알림 큐에서 다음 형식의 테스트 알림이 표시 되어야 합니다.
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>이벤트 알림 가져오기

준비가 되 면 프로젝트에서 [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API를 호출 하 여 일반적인 학습 작업을 수행 합니다.

저장소 알림 큐에서 학습이 완료 되 면 알림을 받게 됩니다.

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`필드는 또는 중 하나일 수 있습니다 `"TrainingCompleted"` `"TrainingFailed"` . `"iterationId"`필드는 학습 된 모델의 ID입니다.

## <a name="get-model-export-backups"></a>모델 내보내기 백업 가져오기

준비가 되 면 [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API를 호출 하 여 학습 된 모델을 지정 된 플랫폼으로 내보냅니다.

지정 된 저장소 컨테이너에서 내보낸 모델의 백업 복사본이 표시 됩니다. Blob 이름의 형식은 다음과 같습니다.

```
{projectId} - {iterationId}.{platformType}
```

또한 내보내기가 완료 되 면 큐에서 알림을 받게 됩니다. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`필드는 또는 중 하나일 수 있습니다 `"ExportCompleted"` `"ExportFailed"` . `"modelUri"`이 필드에는 컨테이너에 저장 된 백업 모델의 URL이 포함 됩니다 .이는 처음에 큐 알림을 통합 했다고 가정 합니다. 이렇게 하지 않으면 필드에 `"modelUri"` Custom Vision 모델 blob에 대 한 SAS URL이 표시 됩니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Custom Vision 리소스 간에 프로젝트를 복사 하 고 이동 하는 방법을 배웠습니다. 다음으로 API 참조 문서를 탐색 하 여 Custom Vision에서 수행할 수 있는 다른 작업을 확인 하세요.
* [REST API 참조 설명서](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
