---
title: Azure DevTest Labs에서 랩의 vnet에 환경 연결 Microsoft Docs
description: '환경 (예: Service Fabric 클러스터)을의 랩 가상 네트워크에 연결 하는 방법을 알아봅니다 Azure DevTest Labs'
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 31f924a4b6d7cbe1b6f71c7e34d43fd9c135bac9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483774"
---
# <a name="connect-an-environment-to-your-labs-virtual-network-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩의 가상 네트워크에 환경 연결
Azure DevTest Labs를 사용 하면 [기본 제공 네트워킹](devtest-lab-configure-vnet.md)을 통해 랩에서 vm을 쉽게 만들 수 있습니다. [다중 VM 환경을 만드는](devtest-lab-test-env.md)기능을 통해 상당한 유연성을 제공 합니다. 이 문서에서는 환경의 Vm을 랩 가상 네트워크에 연결 하는 방법을 보여 줍니다. 이 기능을 사용 하는 한 가지 시나리오는 랩 VNet에 연결 된 SQL Server 데이터 계층을 사용 하 여 N 계층 앱을 설정 하는 것입니다. 랩 VNet에 연결 하 여 랩에 액세스 하는 테스트 Vm을 허용 합니다.  

## <a name="sample-environment-that-uses-lab-vnet"></a>Lab VNet을 사용 하는 샘플 환경
다음은 랩의 서브넷을 연결 하는 간단한 환경 템플릿입니다. 이 샘플에서 `DTLSubnetId` 매개 변수는 랩이 존재 하는 서브넷의 ID를 나타냅니다. `$(LabSubnetId)`DevTest Labs에서 랩 서브넷의 ID로 자동으로 해결 되는에 할당 됩니다. 이 정의에서 VM의 **네트워크 인터페이스** 에 대 한 **서브넷** 속성은 `DTLSubnetId` 동일한 서브넷에 조인 되도록로 설정 됩니다. 

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
이러한 작업을 수행 하려면 Azure Portal를 사용 하는 다음 문서를 참조 하세요. [VM을 다시 시작](devtest-lab-restart-vm.md)합니다.