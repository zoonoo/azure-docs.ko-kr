---
title: Azure Resource Manager 템플릿을 사용하여 Azure Event Grid에서 시스템 토픽 만들기
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Event Grid에서 시스템 토픽을 만드는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 07/22/2021
ms.openlocfilehash: 8420e60dd7336d17845a0a1c6b4e5e6637fe9c8a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469464"
---
# <a name="create-system-topics-in-azure-event-grid-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 Azure Event Grid에서 시스템 토픽 만들기
이 문서에서는 Resource Manager 템플릿을 사용하여 시스템 토픽을 만들고 관리하는 방법을 보여 줍니다. 시스템 토픽에 대한 개요는 [시스템 토픽](system-topics.md)을 참조하세요.

## <a name="create-system-topic-first-and-then-create-event-subscription"></a>시스템 토픽을 만든 다음 이벤트 구독 만들기
먼저 Azure 원본에서 시스템 토픽을 만든 다음 해당 토픽에 대한 이벤트 구독을 만들려면 다음 템플릿을 사용합니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Provide a unique name for the Blob Storage account."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Provide a location for the Blob Storage account that supports Event Grid."
            }
        },
        "eventSubName": {
            "type": "string",
            "defaultValue": "subToStorage",
            "metadata": {
                "description": "Provide a name for the Event Grid subscription."
            }
        },
        "endpoint": {
            "type": "string",
            "metadata": {
                "description": "Provide the URL for the WebHook to receive events. Create your own endpoint for events."
            }
        },
        "systemTopicName": {
            "type": "String",
            "defaultValue": "mystoragesystemtopic",
            "metadata": {
                "description": "Provide a name for the system topic."
            }
        }
    },
    "resources": [
        {
            "name": "[parameters('storageName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-10-01",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "accessTier": "Hot"
            }
        },
        {
            "name": "[parameters('systemTopicName')]",
            "type": "Microsoft.EventGrid/systemTopics",
            "apiVersion": "2020-04-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('storageName')]"
            ],
            "properties": {
                "source": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageName'))]",
                "topicType": "Microsoft.Storage.StorageAccounts"
            }
        },
        {
            "type": "Microsoft.EventGrid/systemTopics/eventSubscriptions",
            "apiVersion": "2020-04-01-preview",
            "name": "[concat(parameters('systemTopicName'), '/', parameters('eventSubName'))]",
            "dependsOn": [
                "[resourceId('Microsoft.EventGrid/systemTopics', parameters('systemTopicName'))]"
            ],
            "properties": {
                "destination": {
                    "properties": {
                        "endpointUrl": "[parameters('endpoint')]"
                    },
                    "endpointType": "WebHook"
                },
                "filter": {
                    "includedEventTypes": [
                        "Microsoft.Storage.BlobCreated",
                        "Microsoft.Storage.BlobDeleted"
                    ]
                }
            }
        }
    ]
}
```

Resource Manager 템플릿을 사용하여 시스템 토픽 및 해당 토픽에 대한 구독을 만드는 방법에 대한 지침은 [Azure Resource Manager 템플릿을 사용하여 웹 엔드포인트로 Blob Storage 이벤트 경로 지정](blob-event-quickstart-template.md)을 참조하세요. 

## <a name="create-system-topic-while-creating-an-event-subscription"></a>이벤트 구독을 만드는 동안 시스템 토픽 만들기 
시스템 토픽을 암시적으로 만들려면 Azure 원본에서 이벤트 구독을 만드는 동안 다음 템플릿을 사용합니다.

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Provide a unique name for the Blob Storage account."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Provide a location for the Blob Storage account that supports Event Grid."
            }
        },
        "eventSubName": {
            "type": "string",
            "defaultValue": "subToStorage",
            "metadata": {
                "description": "Provide a name for the Event Grid subscription."
            }
        },
        "endpoint": {
            "type": "string",
            "metadata": {
                "description": "Provide the URL for the WebHook to receive events. Create your own endpoint for events."
            }
        }
    },
    "resources": [
        {
            "name": "[parameters('storageName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-10-01",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "accessTier": "Hot"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
            "apiVersion": "2018-01-01",
            "dependsOn": [
                "[parameters('storageName')]"
            ],
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "[parameters('endpoint')]"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": [
                        "All"
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계
Azure Event Grid에서 지원되는 시스템 토픽 및 토픽 유형에 대한 자세한 정보는 [Azure Event Grid의 시스템 토픽](system-topics.md) 섹션을 참조하세요. 
