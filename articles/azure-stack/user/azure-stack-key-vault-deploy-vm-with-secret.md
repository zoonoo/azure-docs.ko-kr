---
title: Azure Stack에서 안전 하 게 저장 된 암호를 사용 하 여 VM 배포 | Microsoft Docs
description: Azure Stack Key Vault에 저장 된 암호를 사용 하 여 VM을 배포 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 84ce67fb1790c032931225b76a6c26cecc5a040c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251948"
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Azure Stack Key Vault에 저장 된 보안 암호를 사용 하 여 가상 머신 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

이 문서는 Azure Stack Key Vault에 저장 된 암호를 사용 하 여 Windows Server 가상 컴퓨터를 배포 하는 단계별로 안내 합니다. 키 자격 증명 모음 암호를 사용 하는 것은 일반 텍스트 암호를 전달 하는 것 보다 안전 합니다.

## <a name="overview"></a>개요

Azure Stack key vault에 비밀 형태로 암호와 같은 값을 저장할 수 있습니다. 암호를 만든 후에 Azure Resource Manager 템플릿에서 참조할 수 있습니다. Resource Manager를 사용한 암호를 사용 하 여 다음과 같은 이점이 있습니다.

* 리소스를 배포할 때마다 암호를 수동으로 입력 필요가 없습니다.
* 비밀에 액세스할 수 있는 사용자 또는 서비스 주체를 지정할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Key Vault 서비스를 포함 하는 제품을 구독 해야 합니다.
* [Azure Stack 용 PowerShell을 설치 합니다.](azure-stack-powershell-install.md)
* [PowerShell 환경을 구성 합니다.](azure-stack-powershell-configure-user.md)

다음 단계는 Key Vault에 저장 된 암호를 검색 하 여 가상 컴퓨터를 만드는 데 필요한 프로세스를 설명 합니다.

1. 비밀을 Key Vault를 만듭니다.
2. Azuredeploy.parameters.json 파일을 업데이트 합니다.
3. 템플릿을 배포합니다.

> [!NOTE]  
> VPN을 통해 연결 되어 있는 경우 외부 클라이언트 또는 Azure Stack 개발 키트에서 다음이 단계를 사용할 수 있습니다.

## <a name="create-a-key-vault-secret"></a>Key Vault 비밀 만들기

다음 스크립트를 key vault를 만들고 비밀을 key vault에 암호를 저장 합니다. 사용 된 `-EnabledForDeployment` 키 자격 증명 모음을 만들 때 매개 변수입니다. 이 매개 변수 사용 하면 Azure Resource Manager 템플릿에서 key vault를 참조할 수 있습니다.

```PowerShell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

앞의 스크립트를 실행 하면 출력 비밀 URI를 포함 합니다. 이 URI를 기록해 둡니다. 참조 해야 합니다 [키 자격 증명 모음 템플릿에 암호를 사용 하 여 배포할 Windows 가상 머신](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv)합니다. 다운로드 합니다 [101-vm-보안-암호](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) 개발 컴퓨터에 폴더입니다. 이 폴더에는 `azuredeploy.json` 및 `azuredeploy.parameters.json` 파일을 다음 단계에서 필요 합니다.

수정 된 `azuredeploy.parameters.json` 환경 값에 따라 파일입니다. 특별 한 관심 매개 변수는 자격 증명 모음 이름, 자격 증명 모음 리소스 그룹 및 암호 (이전 스크립트에서 생성) 하는 대로 URI는 합니다. 다음 파일은 매개 변수 파일의 예입니다.

## <a name="update-the-azuredeployparametersjson-file"></a>Azuredeploy.parameters.json 파일 업데이트

SecretName, 사용자 환경에 따라 가상 머신 값의 adminUsername KeyVault URI를 사용 하 여 azuredeploy.parameters.json 파일을 업데이트 합니다. 다음 JSON 파일 템플릿 매개 변수 파일의 예를 보여 줍니다.

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>템플릿 배포

이제 다음 PowerShell 스크립트를 사용 하 여 템플릿을 배포할:

```PowerShell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

템플릿이 성공적으로 배포 되 면 다음과 같이 출력 결과:

![배포 출력](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>다음 단계

* [Key Vault를 사용 하 여 샘플 앱 배포](azure-stack-key-vault-sample-app.md)
* [Key Vault 인증서를 사용 하 여 VM 배포](azure-stack-key-vault-push-secret-into-vm.md)
