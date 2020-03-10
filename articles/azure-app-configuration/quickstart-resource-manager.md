---
title: Azure App Configuration을 사용하여 자동화된 VM 배포 빠른 시작
description: 이 빠른 시작에서는 Azure PowerShell 모듈 및 Azure Resource Manager 템플릿을 사용하여 Azure App Configuration 저장소를 배포하는 방법을 보여 줍니다. 그런 다음, 저장소의 값을 사용하여 VM을 배포합니다.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom: mvc
ms.openlocfilehash: 7c6f4b2ea9494c004067a8b19df2c2f098ac2b7f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274512"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 자동화된 VM 배포

Azure PowerShell 모듈은 PowerShell cmdlet 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 빠른 시작에서는 Azure PowerShell 및 Azure Resource Manager 템플릿을 사용하여 Azure App Configuration 저장소를 배포하는 방법을 보여 줍니다. 그런 다음, 저장소의 키-값을 사용하여 VM을 배포하는 방법을 알아봅니다.

필수 구성 요소 템플릿을 사용하여 App Configuration 저장소를 만든 다음, Azure Portal 또는 Azure CLI를 사용하여 키-값을 저장소에 추가합니다. 기본 템플릿은 기존 구성 저장소의 기존 키-값 구성을 참조합니다. 검색된 값은 이 예제의 VM과 같이 템플릿에서 만든 리소스의 속성을 설정하는 데 사용됩니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

* 이 빠른 시작에서는 Azure PowerShell 모듈이 필요합니다. 로컬 컴퓨터에 설치된 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-Az-ps)를 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 팝업 브라우저에 Azure 자격 증명을 입력합니다.

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

둘 이상의 구독이 있는 경우 다음 cmdlet을 실행하여 이 빠른 시작에 사용할 구독을 선택합니다. `<your subscription name>`을 사용자의 구독 이름으로 바꾸어야 합니다.

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Azure App Configuration 저장소 배포

키-값을 VM에 적용하려면 먼저 기존 Azure App Configuration 저장소가 있어야 합니다. 이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 Azure App Configuration 저장소를 배포하는 방법에 대해 자세히 설명합니다. 이미 App Configuration 저장소가 있는 경우 이 문서의 다음 섹션으로 이동할 수 있습니다. 

1. 다음 json 코드를 복사하여 *prereq.azuredeploy.json*이라는 새 파일에 붙여넣습니다.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "free",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. 다음 json 코드를 복사하여 *prereq.azuredeploy.parameters.json*이라는 새 파일에 붙여넣습니다. **GET-UNIQUE**를 구성 저장소의 고유한 이름으로 바꿉니다.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. PowerShell 창에서 다음 명령을 실행하여 Azure App Configuration 저장소를 배포합니다. 리소스 그룹 이름, 템플릿 파일 경로 및 템플릿 매개 변수 파일 경로를 바꿔야 합니다.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>VM 구성 키-값 추가

Azure Resource Manager 템플릿을 사용하여 App Configuration 저장소를 만들 수 있지만, Azure Portal 또는 Azure CLI를 사용하여 키-값을 추가해야 합니다. 이 빠른 시작에서는 Azure Portal을 사용하여 키-값을 추가합니다.

1. 배포가 완료되면 [Azure Portal](https://portal.azure.com)에서 새로 만든 App Configuration 저장소로 이동합니다.

1. **설정** > **선택키**를 차례로 선택합니다. 기본 읽기 전용 키 연결 문자열을 기록해 둡니다. 이 연결 문자열은 나중에 사용자가 만든 App Configuration 저장소와 통신하도록 애플리케이션을 구성하는 데 사용합니다.

1. **구성 탐색기** > **만들기**를 차례로 선택하여 다음 키-값 쌍을 추가합니다.

   |키|값|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   **레이블**에 대해 *템플릿*을 입력하지만 **콘텐츠 형식**은 비어 있는 상태로 유지합니다.

## <a name="deploy-vm-using-stored-key-values"></a>저장된 키-값을 사용하여 VM 배포

이제 키-값을 저장소에 추가했으므로 Azure Resource Manager 템플릿을 사용하여 VM을 배포할 준비가 되었습니다. 템플릿은 사용자가 만든 **windowsOsVersion** 및 **diskSizeGB** 키를 참조합니다.

1. 다음 json 코드를 복사하여 *azuredeploy.json*이라는 새 파일에 붙여넣거나, [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json)에서 파일을 다운로드합니다.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-02-01-preview', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. 다음 json 코드를 복사하여 *azuredeploy.parameters.json*이라는 새 파일에 붙여넣거나, [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json)에서 파일을 다운로드합니다.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   템플릿의 매개 변수 값을 다음 값으로 바꿉니다.

   |매개 변수|값|
   |-|-|
   |adminPassword|VM에 대한 관리자 암호입니다.|
   |appConfigStoreName|Azure App Configuration 저장소의 이름입니다.|
   |appConfigStoreResourceGroup|App Configuration 저장소가 포함된 리소스 그룹입니다.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|VM에 대한 관리자 사용자 이름입니다.|
   |storageAccountName|VM과 연결된 스토리지 계정에 대한 고유 이름입니다.|
   |domainNameLabel|고유한 도메인 이름입니다.|

1. PowerShell 창에서 다음 명령을 실행하여 Azure App Configuration 저장소를 배포합니다. 리소스 그룹 이름, 템플릿 파일 경로 및 템플릿 매개 변수 파일 경로를 바꿔야 합니다.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

축하합니다! Azure App Configuration에 저장된 구성을 사용하여 VM을 배포했습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, App Configuration 저장소, VM 및 모든 관련 리소스를 삭제합니다. 나중에 App Configuration 저장소 또는 VM을 사용하려는 경우 삭제를 건너뛸 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음과 같은 cmdlet을 실행하여 빠른 시작에서 만든 리소스를 모두 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Resource Manager 템플릿 및 Azure App Configuration의 키-값을 사용하여 VM을 배포했습니다.

Azure App Configuration을 사용하여 다른 애플리케이션을 만드는 방법에 대해 자세히 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [빠른 시작: Azure App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)
