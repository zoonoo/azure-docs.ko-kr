---
title: "Azure 스택에 안전 하 게 저장 된 인증서와 함께 가상 컴퓨터를 배포 | Microsoft Docs"
description: "가상 컴퓨터를 배포 하 고 놓아서 인증서 스택 Azure 주요 자격 증명 모음을 사용 하 여 강제 하는 방법에 알아봅니다"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: 29ccdc9eca9911b2f550f9e09da83d0b1d30f9db
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>가상 컴퓨터 만들기 및 주요 자격 증명 모음에서 검색 된 인증서를 포함 합니다.

이 문서에서는 Azure 스택 및 놓아서 푸시 인증서에서 가상 컴퓨터를 만들 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* 키 자격 증명 모음 서비스를 포함 하는 제품에 등록 해야 해야 합니다. 
* [Azure 스택에 대 한 PowerShell을 설치 합니다.](azure-stack-powershell-install.md)  
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)

Azure 스택의 주요 자격 증명 모음 인증서를 저장 하는 데 사용 됩니다. 인증서는 다양 한 시나리오에서 유용 합니다. 예를 들어 있는 가상 컴퓨터에 있는 인증서가 필요 하는 응용 프로그램을 실행 중인 Azure 스택 하는 시나리오를 살펴보겠습니다. 이 인증서는 웹 사이트에서 SSL 또는 Active Directory에 인증 하기 위해 암호화 하는 데 사용할 수 있습니다. 주요 자격 증명 모음 사용 하면에서 인증서가 있는지 확인 하는 안전 합니다.

이 문서에서는 우리 과정을 단계별로 스택에서 Azure Windows 가상 컴퓨터에 인증서를 강제 하는 데 필요한 단계. VPN을 통해 연결 되어 있는 경우 다음이 단계를 통해 외부 액세스 또는 Azure 스택 개발 키트에서 사용할 수 있습니다.

다음 단계는 가상 컴퓨터에 인증서를 푸시하는 데 필요한 프로세스를 설명 합니다.

1. 비밀 키 자격 증명 모음을 만듭니다.
2. Azuredeploy.parameters.json 파일을 업데이트 합니다.
3. 템플릿 배포

## <a name="create-a-key-vault-secret"></a>키 자격 증명 모음 암호 만들기

다음 스크립트는.pfx 형식으로 인증서를 만듭니다 주요 자격 증명 모음을 만들고는 암호로 키 자격 증명 모음에 인증서를 저장 합니다. 사용 해야 합니다는 `-EnabledForDeployment` 주요 자격 증명 모음을 만들 때 매개 변수입니다. 이 매개 변수는 키 자격 증명 모음에서 Azure 리소스 관리자 템플릿을 참조할 수 있도록 합니다.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

앞의 스크립트를 실행 하면 출력 비밀 URI를 포함 합니다. 이 URI를 적어 둡니다. 참조 해야는 [푸시 인증서를 Windows 리소스 관리자 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate)합니다. 다운로드는 [푸시 인증서 windows vm 템플릿을](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) 개발 컴퓨터에는 폴더입니다. 이 폴더에는 `azuredeploy.json` 및 `azuredeploy.parameters.json` 파일을 다음 단계에서 필요 합니다.

수정 된 `azuredeploy.parameters.json` 환경 값에 따라 파일입니다. 특별 한 관심의 매개 변수는 자격 증명 모음 이름, 자격 증명 모음 리소스 그룹 및 암호 (앞의 스크립트에서 생성) 대로 URI입니다. 다음 파일은 매개 변수 파일의 예:

## <a name="update-the-azuredeployparametersjson-file"></a>Azuredeploy.parameters.json 파일 업데이트

Azuredeploy.parameters.json 파일은 vaultName, 비밀 URI, VmName, 및 사용자 환경에 따라 다른 값을 업데이트 합니다. 다음 JSON 파일 템플릿 매개 변수 파일의 예를 보여 줍니다. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>템플릿 배포

다음 PowerShell 스크립트를 사용 하 여 템플릿을 배포 이제:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

템플릿이 성공적으로 배포 되 면 그 결과 다음과 같은 출력:

![배포 출력](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

이 가상 컴퓨터를 배포할 때 Azure 스택 가상 컴퓨터에 인증서를 푸시합니다. Windows에서 인증서는 LocalMachine 인증서의 위치에 있는 사용자 제공 되는 인증서 저장소에 추가 됩니다. Linux에서 인증서 아래에 배치 됩니다 /var/lib/waagent 디렉터리에 파일 이름의 &lt;UppercaseThumbprint&gt;.crt X509에 대 한 인증서 파일 및 &lt;UppercaseThumbprint&gt;.prv 개인 키에 대 한 .

## <a name="retire-certificates"></a>인증서를 사용 중지

이전 섹션에서 살펴본를 가상 컴퓨터에 새 인증서를 강제 하는 방법입니다. 가상 컴퓨터에서 여전히 이전 인증서 이며 제거할 수 없습니다. 그러나 사용 하 여 이전 버전의 보안을 비활성화할 수 있습니다는 `Set-AzureKeyVaultSecretAttribute` cmdlet. 다음은이 cmdlet 사용 하는 예입니다. 자격 증명 모음 이름, 암호 이름 및 사용자 환경에 따라 버전 값을 교체할 수 있는지 확인 합니다.

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>다음 단계

* [Key Vault 암호를 사용하여 VM 배포](azure-stack-kv-deploy-vm-with-secret.md)
* [응용 프로그램이 키 자격 증명 모음에 액세스 하도록 허용](azure-stack-kv-sample-app.md)


