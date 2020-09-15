---
title: Custom Vision 프로젝트 복사 및 이동
titleSuffix: Azure Cognitive Services
description: ExportProject 및 ImportProject Api를 사용 하 여 Custom Vision 프로젝트를 복사 하 고 이동 하는 방법에 대해 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 5285dfb23476662a13162788b2ec497b4fe49228
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532698"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Custom Vision 프로젝트 복사 및 이동

Custom Vision 프로젝트를 만들고 학습 한 후에는 프로젝트를 다른 리소스로 복사할 수 있습니다. 예를 들어 개발 환경에서 프로덕션 환경으로 프로젝트를 이동 하거나 데이터 보안을 강화 하기 위해 다른 Azure 지역의 계정에 프로젝트를 백업할 수 있습니다.

**[Exportproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3])** 및 **[importproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** api는 한 Custom Vision 계정에서 다른 계정으로 프로젝트를 복사할 수 있도록 하 여이 시나리오를 가능 하 게 합니다. 이 가이드에서는 이러한 REST Api를 사용 하는 방법을 보여 줍니다. Postman과 같은 HTTP 요청 서비스를 사용 하 여 요청을 실행할 수도 있습니다.

## <a name="business-scenarios"></a>비즈니스 시나리오

앱 또는 비즈니스가 Custom Vision 프로젝트 사용에 따라 달라 지는 경우 다른 지역의 다른 Custom Vision 계정으로 모델을 복사 하는 것이 좋습니다. 그런 다음 지역 중단이 발생 하면 복사 된 지역에서 프로젝트에 액세스할 수 있습니다.

##  <a name="prerequisites"></a>사전 요구 사항

- 두 개의 Azure Custom Vision 리소스 없는 경우 Azure Portal으로 이동 하 여 [새 Custom Vision 리소스를 만듭니다](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true).
- Custom Vision 리소스의 학습 키 및 끝점 Url입니다. Azure Portal의 리소스 **개요** 탭에서 이러한 값을 찾을 수 있습니다.
- Custom Vision 프로젝트를 만들었습니다. 이 작업을 수행 하는 방법에 대 한 지침은 [분류자 빌드를](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/getting-started-build-a-classifier) 참조 하세요.

## <a name="process-overview"></a>프로세스 개요

프로젝트를 복사 하는 프로세스는 다음 단계로 구성 됩니다.

1. 먼저 복사 하려는 원본 계정에서 프로젝트의 ID를 가져옵니다.
1. 그런 다음, 프로젝트 ID와 원본 계정의 학습 키를 사용 하 여 **exportproject** API를 호출 합니다. 임시 토큰 문자열을 가져옵니다.
1. 그런 다음 대상 계정의 토큰 문자열 및 학습 키를 사용 하 여 **Importproject** API를 호출 합니다. 그러면 프로젝트가 대상 계정 아래에 나열 됩니다.

## <a name="get-the-project-id"></a>프로젝트 ID 가져오기

먼저 **[Getprojects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** 를 호출 하 여 기존 Custom Vision 프로젝트 및 해당 id의 목록을 확인 합니다. 원본 계정의 학습 키 및 끝점을 사용 합니다.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

`200\OK`본문에서 프로젝트 및 해당 메타 데이터 목록이 포함 된 응답을 받게 됩니다. `"id"`값은 다음 단계에 대해 복사할 문자열입니다.

```json
[
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
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>프로젝트 내보내기

프로젝트 ID와 소스 학습 키 및 끝점을 사용 하 여 **[Exportproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** 를 호출 합니다.

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

`200/OK`내보낸 프로젝트와 참조 문자열에 대 한 메타 데이터가 포함 된 응답을 받게 됩니다 `"token"` . 토큰의 값을 복사 합니다.

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>프로젝트 가져오기

참조 토큰과 함께 대상 학습 키 및 끝점을 사용 하 여 **[Importproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** 를 호출 합니다. 새 계정에서 프로젝트에 이름을 지정할 수도 있습니다.

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects/import?token={token}?name={name}"
-H "Training-key: {training key}"
```

`200/OK`새로 가져온 프로젝트에 대 한 메타 데이터가 포함 된 응답을 받게 됩니다.

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
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 Custom Vision 리소스 간에 프로젝트를 복사 하 고 이동 하는 방법을 배웠습니다. 다음으로 API 참조 문서를 탐색 하 여 Custom Vision에서 수행할 수 있는 다른 작업을 확인 하세요.
* [REST API 참조 설명서](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
