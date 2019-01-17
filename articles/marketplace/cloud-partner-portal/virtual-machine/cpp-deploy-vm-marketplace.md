---
title: Azure Marketplace에서 VM 배포 | Microsoft Docs
description: Azure Marketplace에서 미리 구성된 가상 머신을 배포하는 방법을 설명합니다.
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
ms.openlocfilehash: 3ec4e0b047e94dc7481c51390c6e4370fdea5efa
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258804"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Azure Marketplace에서 가상 머신 배포

이 문서에서는 제공된 Azure PowerShell 스크립트를 사용하여 Azure Marketplace에서 미리 구성된 VM(가상 머신)을 배포하는 방법을 설명합니다.  이 스크립트는 또한 VM에 WinRM HTTP 및 HTTPS 엔드포인트를 노출합니다.  이 스크립트를 사용하려면 [Azure Key Vault용 인증서 만들기](./cpp-create-key-vault-cert.md)에 설명된 대로 Azure Key Vault에 업로드된 인증서가 이미 있어야 합니다. 


## <a name="vm-deployment-template"></a>VM 배포 템플릿

빠른 시작 Azure VM 배포 템플릿은 온라인 파일 [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json)으로 사용할 수 있습니다.  여기에는 다음 매개 변수가 포함되어 있습니다.

|  **매개 변수**        |   **설명**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | 저장소 계정 이름                       |
| dnsNameForPublicIP    | 공용 IP에 대한 DNS 이름입니다. 소문자여야 합니다.    |
| adminUserName         | 관리자의 사용자 이름입니다.                          |
| adminPassword         | 관리자 암호입니다.                          |
| imagePublisher        | 이미지 게시자입니다.                                   |
| imageOffer            | 이미지 제품입니다.                                       |
| imageSKU              | 이미지 SKU입니다.                                         |
| vmSize                | VM의 크기입니다.                                    |
| vmName                | VM의 이름                                    |
| vaultName             | Key Vault의 이름입니다.                             |
| vaultResourceGroup    | Key Vault의 리소스 그룹입니다.                   |
| certificateUrl        | KeyVault의 버전을 포함하는 인증서의 URL(예: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`)입니다. |
|  |  |


## <a name="deployment-script"></a>배포 스크립트

다음 Azure PowerShell 스크립트를 편집하고 실행하여 지정된 Azure Marketplace VM을 배포합니다.

```powershell

New-AzureRmResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>다음 단계

미리 구성된 VM을 배포한 후 포함된 솔루션을 구성 및 액세스하거나 후속 개발에 사용할 수 있습니다. 
