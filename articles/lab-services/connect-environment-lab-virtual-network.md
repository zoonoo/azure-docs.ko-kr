---
title: Azure DevTest 랩에서 환경을 랩의 vnet에 연결 | 마이크로 소프트 문서
description: 'Azure DevTest Labs에서 환경(예: 서비스 패브릭 클러스터)을 랩의 가상 네트워크에 연결하는 방법을 알아봅니다.'
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: bb8b5f7d6578390fd0f48c3de154cfdb034ac6c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60777171"
---
# <a name="connect-an-environment-to-your-labs-virtual-network-in-azure-devtest-labs"></a>Azure DevTest 랩에서 환경을 랩의 가상 네트워크에 연결합니다.
Azure DevTest 연구소를 사용하면 [기본 제공 네트워킹이](devtest-lab-configure-vnet.md)있는 랩에서 VM을 쉽게 만들 수 있습니다. [다중 VM 환경을 만들](devtest-lab-test-env.md)수 있는 유연성이 매우 큽니다. 이 문서에서는 환경에서 VM을 랩 가상 네트워크에 연결하는 방법을 보여 주며 이 문서에서는 이 기능을 사용하는 한 가지 시나리오는 랩VNet에 연결된 SQL Server 데이터 계층을 사용하여 N 계층 앱을 설정하여 랩의 테스트 VM이 액세스할 수 있도록 하는 것입니다.  

## <a name="sample-environment-that-uses-lab-vnet"></a>랩 VNet을 사용하는 샘플 환경
다음은 랩의 서브넷을 연결하는 간단한 환경 템플릿입니다. 이 샘플에서 `DTLSubnetId` 매개 변수는 랩이 있는 서브넷의 ID를 나타냅니다. 에 할당됩니다: `$(LabSubnetId)`에 할당됩니다. 이 정의에서 VM네트워크 **인터페이스의** **서브넷** 속성은 `DTLSubnetId` 동일한 서브넷을 조인하도록 설정됩니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DTLEnvironVmStoretype": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_ZRS",
                "Standard_GRS",
                "Standard_RAGRS",
                "Premium_LRS"
            ]
        },
        "DTLEnvironVmName": {
            "type": "string",
            "minLength": 1
        },
        "VmAdminUserName": {
            "type": "string",
            "minLength": 1
        },
        "VmAdminUserPassword": {
            "type": "securestring"
        },
        "DTLEnvironVmOsVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1",
                "2012-Datacenter",
                "2012-R2-Datacenter",
                "Windows-Server-Technical-Preview"
            ]
        },
        "DTLSubnetId": {
            "type": "string",
            "defaultValue": "$(LabSubnetId)"
        }
    },
    "variables": {
        "DTLEnvironStoreName": "[toLower([concat(parameters('DTLEnvironVmName'), 'storename')])]",
        "DTLEnvironVmImagePublisher": "MicrosoftWindowsServer",
        "DTLEnvironVmImageOffer": "WindowsServer",
        "DTLEnvironVmOSDiskName": "[concat(parameters('DTLEnvironVmName'), 'OSDisk')]",
        "DTLEnvironVmSize": "Standard_D2_v2",
        "DTLEnvironVmStorageAccountContainerName": "vhds",
        "DTLEnvironVmNicName": "[concat(parameters('DTLEnvironVmName'), 'NetworkInterface')]"
    },
    "resources": [{
            "name": "[variables('DTLEnvironStoreName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[resourceGroup().location]",
            "apiVersion": "2016-01-01",
            "sku": {
                "name": "[parameters('DTLEnvironVmStoretype')]"
            },
            "dependsOn": [],
            "tags": {
                "displayName": "[variables('DTLEnvironStoreName')]"
            },
            "kind": "Storage"
        },
        {
            "name": "[variables('DTLEnvironVmNicName')]",
            "type": "Microsoft.Network/networkInterfaces",
            "location": "southeastasia",
            "apiVersion": "2016-03-30",
            "dependsOn": [],
            "tags": {
                "displayName": "[variables('DTLEnvironVmNicName')]"
            },
            "properties": {
                "ipConfigurations": [{
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "[parameters('DTLSubnetId')]"
                        }
                    }
                }]
            }
        },
        {
            "name": "[parameters('DTLEnvironVmName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "location": "[resourceGroup().location]",
            "apiVersion": "2015-06-15",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', variables('DTLEnvironStoreName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces', variables('DTLEnvironVmNicName'))]"
            ],
            "tags": {
                "displayName": "[parameters('DTLEnvironVmName')]"
            },
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('DTLEnvironVmSize')]"
                },
                "osProfile": {
                    "computerName": "[parameters('DTLEnvironVmName')]",
                    "adminUsername": "[parameters('VmAdminUserName')]",
                    "adminPassword": "[parameters('VmAdminUserPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('DTLEnvironVmImagePublisher')]",
                        "offer": "[variables('DTLEnvironVmImageOffer')]",
                        "sku": "[parameters('DTLEnvironVmOsVersion')]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "name": "[variables('DTLEnvironVmOSDiskName')]",
                        "vhd": {
                            "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('DTLEnvironStoreName')), '2016-01-01').primaryEndpoints.blob, variables('DTLEnvironVmStorageAccountContainerName'), '/', variables('DTLEnvironVmOSDiskName'), '.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [{
                        "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('DTLEnvironVmNicName'))]"
                    }]
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="next-steps"></a>다음 단계
이러한 작업을 수행 하려면 Azure 포털을 사용 하 여 다음 문서를 참조: [VM 을 다시 시작](devtest-lab-restart-vm.md)합니다.