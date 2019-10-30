---
author: lugoldbemicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 10/23/2019
ms.author: lugoldbe
ms.openlocfilehash: 9aca01b24bc3351460c462c08689fdaa41e3a755
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73057287"
---
## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

이 문서에서는 Azure Resource Manager 템플릿을 사용 하 여 리소스 그룹, 저장소 계정 및 컨테이너, 이벤트 허브 및 Azure 데이터 탐색기 클러스터와 데이터베이스를 만듭니다. 코드 예제를 실행 하는 데 사용 되는 이름이 `template.json`인 파일에 다음 콘텐츠를 저장 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Specifies a the event hub Namespace name."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Specifies a event hub name."
            }
        },
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": ["Standard_LRS", "Standard_GRS", "Standard_ZRS", "Premium_LRS"],
            "metadata": {
                "description": "Storage Account type"
            }
        },
        "storageAccountName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Name of the storage account to create"
            }
        },
        "containerName": {
            "type": "string",
            "defaultValue": "[concat('storagecontainer', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Name of the container in storage account to create"
            }
        },
        "eventHubSku": {
            "type": "string",
            "allowedValues": ["Basic", "Standard"],
            "defaultValue": "Standard",
            "metadata": {
                "description": "Specifies the messaging tier for service Bus namespace."
            }
        },
        "kustoClusterName": {
            "type": "string",
            "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Name of the cluster to create"
            }
        },
        "kustoDatabaseName": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Name of the database to create"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "apiVersion": "2017-04-01",
            "type": "Microsoft.EventHub/namespaces",
            "name": "[parameters('eventHubNamespaceName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('eventHubSku')]",
                "tier": "[parameters('eventHubSku')]",
                "capacity": 1
            },
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            }
        }, {
            "apiVersion": "2017-04-01",
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventHubNamespaceName'), '/', parameters('eventHubName'))]",
            "location": "[parameters('location')]",
            "dependsOn": ["[resourceId('Microsoft.EventHub/namespaces', parameters('eventHubNamespaceName'))]"],
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1
            }
        }, {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "resources": [
                {
                    "name": "[concat('default/', parameters('containerName'))]",
                    "type": "blobServices/containers",
                    "apiVersion": "2018-07-01",
                    "dependsOn": [
                        "[parameters('storageAccountName')]"
                    ],
                    "properties": {
                        "publicAccess": "None"
                    }
                }
            ],
            "properties": {}
        }, {
            "name": "[parameters('kustoClusterName')]",
            "type": "Microsoft.Kusto/clusters",
            "sku": {
                "name": "Standard_D13_v2",
                "tier": "Standard",
                "capacity": 2
            },
            "apiVersion": "2019-05-15",
            "location": "[parameters('location')]",
            "tags": {
                "Created By": "GitHub quickstart template"
            }
        }, {
            "name": "[concat(parameters('kustoClusterName'), '/', parameters('kustoDatabaseName'))]",
            "type": "Microsoft.Kusto/clusters/databases",
            "apiVersion": "2019-05-15",
            "location": "[parameters('location')]",
            "dependsOn": ["[resourceId('Microsoft.Kusto/clusters', parameters('kustoClusterName'))]"],
            "properties": {
                "softDeletePeriodInDays": 365,
                "hotCachePeriodInDays": 31
            }
        }
    ]
}

```
