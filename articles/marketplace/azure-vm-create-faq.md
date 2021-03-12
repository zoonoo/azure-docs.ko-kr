---
title: Azure Marketplace VM에 대 한 일반적인 질문
description: Azure Marketplace에서 가상 머신을 만들 때 발생 하는 일반적인 질문입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 09644747c36b5406960097e52bbeeef1fa157e89
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102629996"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Azure Marketplace VM에 대 한 일반적인 질문

FAQ (질문과 대답)는 Azure Marketplace에서 VM (가상 머신) 제품을 만들 때 발생할 수 있는 일반적인 문제를 다룹니다.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>내 VM에서 작동하도록 VPN(가상 사설망)을 구성하려면 어떻게 해야 하나요?

Azure Resource Manager 배포 모델을 사용하는 경우 세 가지 옵션은 다음과 같습니다.

- [Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Azure PowerShell을 사용하여 경로 기반 VPN 게이트웨이 만들기](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [CLI를 사용하여 경로 기반 VPN 게이트웨이 만들기](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure 기반 VM에서 Microsoft 서버 소프트웨어를 실행하기 위한 Microsoft 지원 정책은 무엇인가요?

[Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)에서 자세한 내용을 확인할 수 있습니다.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM에서 시작 태스크에 있는 사용자 지정 스크립트 확장을 어떻게 관리하나요?

Azure PowerShell 모듈, Azure Resource Manager 템플릿을 통해 사용자 지정 스크립트 확장을 사용하는 방법과 Windows 시스템의 문제 해결 단계에 대한 자세한 내용은 [Windows용 사용자 지정 스크립트 확장](../virtual-machines/extensions/custom-script-windows.md)을 참조하세요.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace에서 32비트 애플리케이션 또는 서비스가 지원되나요?

아니요. Azure VM에 지원되는 운영 체제 및 표준 서비스는 모두 64비트입니다. 대부분의 64비트 운영 체제는 이전 버전과의 호환성을 위해 32비트 버전의 애플리케이션을 지원하지만, VM 솔루션의 일부로 32비트 애플리케이션을 사용하는 것은 지원되지 않으며 권장되지 않습니다. 애플리케이션을 64비트 프로젝트로 다시 만드세요.

자세한 내용은 다음 문서를 참조하세요.

- [32비트 애플리케이션 실행](/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 가상 머신에 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>오류: VHD가 이미 리소스로 이미지 리포지토리에 등록됨

내 VHD에서 이미지를 만들려고 할 때마다 Azure PowerShell에서 "리소스인 이미지 리포지토리로 VHD를 이미 등록했습니다." 오류가 표시됩니다. 이전에 이미지를 만들지도 않았고, Azure에서 이 이름을 가진 이미지를 찾지도 못했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 문제는 일반적으로 사용자가 잠겨 있는 VHD에서 VM을 만든 경우에 발생합니다. 이 VHD에서 할당된 VM이 없는지 확인한 다음, 작업을 다시 시도하세요. 문제가 계속 발생하면 지원 티켓을 여세요. [파트너 센터 지원](support.md)을 참조하세요.

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>일반화 된 vhd에서 VM을 만들 어떻게 할까요? 있나요?

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 준비

이 섹션에서는 사용자 제공 VM(가상 머신) 이미지를 만들고 배포하는 방법을 설명합니다. Azure에서 배포 된 가상 하드 디스크에서 운영 체제 및 데이터 디스크 VHD 이미지를 제공 하 여이 작업을 수행할 수 있습니다. 이러한 단계는 일반화 된 VHD를 사용 하 여 VM을 배포 합니다.

1. Azure Portal에 로그인합니다.
2. 일반화 된 운영 체제 VHD 및 데이터 디스크 Vhd를 Azure Storage 계정에 업로드 합니다.
3. 홈 페이지에서 리소스 만들기를 선택 하 고 "템플릿 배포"를 검색 한 후 만들기를 선택 합니다.
4. 편집기에서 사용자 고유의 템플릿 빌드를 선택합니다.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="템플릿 선택을 표시 합니다.":::

5. 다음 JSON 템플릿을 편집기에 붙여넣고 저장을 선택 합니다.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. 표시된 사용자 지정 배포 속성 페이지에 대한 매개 변수 값을 제공합니다.

 **표 1**

| **ResourceGroupName** | **기존 Azure 리소스 그룹 이름입니다. 일반적으로 키 자격 증명 모음과 동일한 RG를 사용 합니다.** |
| --- | --- |
| TemplateFile | VHDtoImage.json 파일에 대한 전체 경로 이름입니다. |
| userStorageAccountName | 스토리지 계정 이름 |
| dnsNameForPublicIP | 퍼블릭 IP의 DNS 이름입니다. 소문자여야 합니다. |
| subscriptionId | Azure 구독 식별자입니다. |
| 위치 | 리소스 그룹의 표준 Azure 지리적 위치입니다. |
| vmName | 가상 머신의 이름입니다. |
| vhdUrl | 가상 하드 디스크의 웹 주소입니다. |
| vmSize | 가상 머신 인스턴스의 크기입니다. |
| publicIPAddressName | 퍼블릭 IP 주소의 이름입니다. |
| virtualNetworkName | 가상 네트워크의 이름입니다. |
| nicName | 가상 네트워크의 네트워크 인터페이스 카드 이름입니다. |
| adminUserName | 관리자 계정의 사용자 이름입니다. |
| adminPassword | 관리자 암호입니다. |
|

7. 이러한 값을 입력한 후 구매를 선택합니다.

Azure에서 배포가 시작됩니다. 지정된 관리되지 않는 VHD가 있는 새 VM을 지정된 스토리지 계정 경로에 만듭니다. 포털의 왼쪽에 있는 Virtual Machines를 선택하여 Azure Portal에서 진행 상황을 추적할 수 있습니다. VM을 만들면 상태가 시작 중에서 실행 중으로 변경됩니다.

2 세대 VM 배포의 경우 다음 템플릿을 사용 합니다.
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>PowerShell을 사용 하 여 Azure VM 배포

다음 스크립트를 복사 및 편집하여 `$storageaccount` 및 `$vhdUrl` 변수의 값을 제공합니다. 이 스크립트를 실행하여 일반화된 기존 VHD에서 Azure VM 리소스를 만듭니다.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```


## <a name="next-steps"></a>다음 단계

- [VM 인증 문제 해결](azure-vm-create-certification-faq.md)
