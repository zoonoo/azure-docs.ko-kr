---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: REST
ms.openlocfilehash: 0d248fa57d5288cedae8dc46441739575c48c129
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918224"
---
<!--Create a copyAllNonInterleave transform with REST-->

다음 Azure REST 명령은 `copyAllBitrateNonInterleaved` 사전 설정에서를 사용 하 여 변환을 만듭니다 `#Microsoft.Media.BuiltInStandardEncoderPreset` . `subscriptionID`, 및 값을 `resourceGroup` `accountName` 현재 작업 중인 값으로 바꿉니다. 을 설정 하 여 변환 이름을 지정 `transformName` 합니다. 

사용 가능한 모든 REST API 변환을 보려면 [변환-만들기 또는 업데이트](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#definitions)를 참조 하세요.

```REST
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>본문

```json
{
    "name": "myTransform",
    "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "description": "Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                    "presetName": "CopyAllBitrateNonInterleaved"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>샘플 응답
```json
{
  "name":"myTransform",
    "id":"/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform","type":"Microsoft.Media/mediaservices/transforms","properties":{
        "created":"2020-11-18T19:19:27.4405209Z",
        "description":"Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets","lastModified":"2020-11-18T19:19:27.4405209Z",
        "outputs":[
            {
                "onError":"StopProcessingJob","relativePriority":"Normal","preset":{
                "@odata.type":"#Microsoft.Media.BuiltInStandardEncoderPreset",
                "presetName":"CopyAllBitrateNonInterleaved"
        }
      }
    ]
  }
}
```
