---
title: Azure Resource Manager 템플릿을 사용 하 여 Azure Event Grid에서 시스템 항목을 만듭니다.
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 Azure Event Grid에서 시스템 항목을 만드는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1c8881a2d9dfca43084cc537b106e84b050a18d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115165"
---
# <a name="create-system-topics-in-azure-event-grid-using-resource-manager-templates"></a>리소스 관리자 템플릿을 사용 하 여 Azure Event Grid에서 시스템 항목 만들기
이 문서에서는 리소스 관리자 템플릿을 사용 하 여 시스템 항목을 만들고 관리 하는 방법을 보여 줍니다. 시스템 항목의 개요는 [시스템 항목](system-topics.md)을 참조 하십시오.

## <a name="create-system-topic-first-and-then-create-event-subscription"></a>먼저 시스템 항목을 만든 다음 이벤트 구독 만들기
먼저 Azure 원본에서 시스템 항목을 만든 다음 해당 토픽에 대 한 이벤트 구독을 만들려면 다음과 같은 템플릿을 사용할 수 있습니다. 

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
                "description": "Provide a name for the system topic."
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
                "source": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageName'))]",
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

리소스 관리자 템플릿을 사용 하 여 시스템 토픽 및 구독을 만드는 방법에 대 한 지침은 [Azure Resource Manager 템플릿을 사용 하 여 Blob storage 이벤트를 웹 끝점으로 라우팅을](blob-event-quickstart-template.md) 참조 하세요. 

## <a name="create-system-topic-while-creating-an-event-subscription"></a>이벤트 구독을 만드는 동안 시스템 항목 만들기 
시스템 항목을 암시적으로 만들려면 Azure 원본에서 이벤트 구독을 만드는 동안 다음 템플릿을 사용할 수 있습니다.

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
Azure Event Grid에서 지 원하는 시스템 항목과 항목 유형에 대 한 자세한 내용은 [Azure Event Grid의 시스템 항목](system-topics.md) 을 참조 하십시오. 
