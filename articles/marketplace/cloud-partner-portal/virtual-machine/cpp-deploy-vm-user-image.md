---
title: 사용자 VHD에서 Azure VM 배포 | Microsoft Docs
description: 사용자 VHD 이미지를 배포하여 Azure VM 인스턴스를 만드는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: 48be60a7ba5770f8c329cb6323a5caa8fcf7f961
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265059"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>사용자 VHD에서 Azure VM 배포

이 문서에서는 제공된 Azure Resource Manager 템플릿과 Azure PowerShell 스크립트를 통해 일반화된 VHD 이미지를 배포하여 새 Azure VM 리소스를 만드는 방법을 설명합니다.


## <a name="vhd-deployment-template"></a>VHD 배포 템플릿

[VHD 배포](cpp-deploy-json-template.md)용 Azure Resource Manager 템플릿을 `VHDtoImage.json`라는 로컬 파일에 복사합니다.  이 파일을 편집하여 다음 매개 변수 값을 제공합니다. 

|  **매개 변수**             |   **설명**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | 기존 Azure 리소스 그룹 이름입니다.  일반적으로 Key Vault와 연결된 동일한 RG를 사용합니다.  |
| TemplateFile               | 파일 `VHDtoImage.json`의 전체 경로 이름입니다.                                    |
| userStorageAccountName     | 저장소 계정 이름                                                    |
| sNameForPublicIP           | 공용 IP에 대한 DNS 이름입니다. 소문자여야 합니다.                                  |
| subscriptionId             | Azure 구독 식별자입니다.                                                  |
| 위치                   | 리소스 그룹의 표준 Azure 지리적 위치입니다.                       |
| vmName                     | 가상 머신의 이름입니다.                                                    |
| vaultName                  | Key Vault의 이름입니다.                                                          |
| vaultResourceGroup         | Key Vault의 리소스 그룹입니다.
| certificateUrl             | Key Vault에 저장된 버전을 포함하는 인증서의 URL(예: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`) |
| vhdUrl                     | 가상 하드 디스크의 URL입니다.                                                   |
| vmSize                     | 가상 머신 인스턴스의 크기입니다.                                           |
| publicIPAddressName        | 공용 IP 주소의 이름입니다.                                                  |
| virtualNetworkName         | 가상 네트워크의 이름입니다.                                                    |
| nicName                    | 가상 네트워크의 네트워크 인터페이스 카드 이름입니다.                     |
| adminUserName              | 관리자 계정의 사용자 이름입니다.                                          |
| adminPassword              | 관리자 암호입니다.                                                          |
|  |  |


## <a name="powershell-script"></a>PowerShell 스크립트

다음 스크립트를 복사 및 편집하여 `$storageaccount` 및 `$vhdUrl` 변수의 값을 제공합니다.  이 스크립트를 실행하여 일반화된 기존 VHD에서 Azure VM 리소스를 만듭니다.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzureRMResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>다음 단계

VM이 배포되면 [VM 이미지를 인증](./cpp-certify-vm.md)할 준비가 되었습니다.
