---
title: "템플릿을 사용한 Azure Event Grid 구독"
description: "Resource Manager 템플릿을 사용하여 Event Grid 구독을 만듭니다."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2b9f55f8e944d688b622e30a773e1a34698f22ec
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Event Grid 구독에 Resource Manager 템플릿 사용

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Event Grid 구독을 만드는 방법을 보여 줍니다. 사용하는 형식은 리소스 그룹 이벤트를 구독할지 또는 특정 리소스 종류에 대한 이벤트를 구독할지에 따라 다릅니다. 이 문서에는 두 형식이 모두 나와 있습니다.

## <a name="subscribe-to-resource-group-events"></a>리소스 그룹 이벤트 구독

리소스 그룹 이벤트를 구독할 경우 리소스 종류로 `Microsoft.EventGrid/eventSubscriptions`를 사용합니다. 이벤트 지점 종류로는 `WebHook` 또는 `EventHub`를 사용합니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

리소스 그룹에 이 템플릿을 배포할 때 해당 리소스 그룹에 대한 이벤트를 구독하게 됩니다.

## <a name="subscribe-to-resource-events"></a>리소스 이벤트 구독

리소스 이벤트를 구독할 경우 리소스 종류와 이름을 구독 정의에 포함하여 올바른 리소스에 구독을 연결합니다. 리소스 종류로는 `<provider-namespace>/<resource-type>/providers/eventSubscriptions`를 사용합니다. 이름으로는 `<resource-name>/Microsoft.EventGrid/<subscription-name>`을 사용합니다. 이벤트 지점 종류로는 `WebHook` 또는 `EventHub`를 사용합니다.

다음 예제에서는 Blob 저장소 이벤트를 구독하는 방법을 보여 줍니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2017-09-15-preview",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.
* Event Grid를 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.