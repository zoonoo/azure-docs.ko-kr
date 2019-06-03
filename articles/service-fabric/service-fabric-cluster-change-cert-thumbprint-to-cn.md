---
title: 인증서 일반 이름을 사용하도록 Azure Service Fabric 클러스터 업데이트 | Microsoft Docs
description: 인증서 지문 대신 인증서 일반 이름을 사용하도록 Service Fabric 클러스터를 전환하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: aljo
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/01/2019
ms.author: aljo
ms.openlocfilehash: a94fda5a1f3aedd5842bad92b5348a77177b4137
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302454"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>인증서 지문에서 일반 이름으로 클러스터 변경
두 인증서가 동일한 지문을 사용하면 안 됩니다. 이렇게 될 경우 클러스터 인증서가 롤오버되거나 관리에 어려움이 발생합니다. 그러나 여러 인증서가 동일한 일반 이름 또는 제목을 사용하는 것은 가능합니다.  배포된 클러스터를 인증서 지문에서 인증서 일반 이름으로 전환하면 인증서 관리 방법이 훨씬 간단해집니다. 이 문서에서는 인증서 지문 대신 인증서 일반 이름을 사용하도록 실행 중인 Service Fabric 클러스터를 업데이트하는 방법을 설명합니다.

>[!NOTE]
> 템플릿에 두 개의 지문이 선언된 경우 두 개의 배포를 수행해야 합니다.  첫 번째 배포는 이 문서의 단계를 따르기 전에 수행합니다.  첫 번째 배포는 템플릿의 **thumbprint** 속성을 사용 중인 인증서로 설정하고 **thumbprintSecondary** 속성을 제거합니다.  두 번째 배포에서 이 문서의 단계를 수행합니다.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>인증서 얻기
먼저 [CA(인증 기관)](https://wikipedia.org/wiki/Certificate_authority)에서 인증서를 얻습니다.  인증서 일반 이름이 클러스터의 호스트 이름이어야 합니다.  예: "myclustername.southcentralus.cloudapp.azure.com".  

테스트를 위해 무료 또는 오픈 인증 기관에서 CA 서명 인증서를 얻을 수 있습니다.

> [!NOTE]
> Azure Portal에서 Service Fabric 클러스터를 배포할 때 생성된 인증서를 포함하여 자체 서명된 인증서는 지원되지 않습니다.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>인증서를 업로드하고 확장 집합에 설치
Azure에서 Service Fabric 클러스터는 가상 머신 확장 집합에 배포됩니다.  인증서를 키 자격 증명 모음에 업로드하고 클러스터가 실행되고 있는 가상 머신 확장 집합에 인증서를 설치합니다.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> 확장 집합 비밀에서는 두 개의 개별 비밀에 동일한 리소스 ID를 사용할 수 없습니다. 각 비밀은 버전이 관리되는 고유한 리소스이기 때문입니다. 

## <a name="download-and-update-the-template-from-the-portal"></a>포털에서 템플릿을 다운로드하고 업데이트
기본 확장 집합에 인증서가 설치되어 있지만, 해당 인증서 및 일반 이름을 사용하도록 Service Fabric 클러스터를 업데이트해야 합니다.  이제 클러스터 배포에 사용할 템플릿을 다운로드합니다.  에 로그인 합니다 [Azure portal](https://portal.azure.com) 클러스터를 호스팅하는 리소스 그룹으로 이동 합니다.  **설정**에서 **배포**를 선택합니다.  가장 최근 배포를 선택하고 **템플릿 보기**를 클릭합니다.

![템플릿 보기][image1]

로컬 컴퓨터에 템플릿 및 매개 변수 JSON 파일을 다운로드합니다.

먼저 텍스트 편집기에서 매개 변수 파일을 열고 다음 매개 변수 값을 추가합니다.
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

다음으로, 텍스트 편집기에서 템플릿 파일을 열고 인증서 일반 이름을 지원하도록 세 가지를 업데이트합니다.

1. **매개 변수** 섹션에서 *certificateCommonName* 매개 변수를 추가합니다.
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    또한 제거를 고려 합니다 *certificateThumbprint*, Resource Manager 템플릿에서 더 이상 참조할 수 없습니다.

2. **Microsoft.Compute/virtualMachineScaleSets** 리소스에서 지문 대신 인증서 설정의 일반 이름을 사용하도록 가상 머신 확장을 업데이트합니다.  **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate**에서 `"commonNames": ["[parameters('certificateCommonName')]"],`을 추가하고 `"thumbprint": "[parameters('certificateThumbprint')]",`를 제거합니다.
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  **Microsoft.ServiceFabric/clusters** 리소스에서 API 버전을 "2018-02-01"로 업데이트합니다.  또한 다음 예제와 같이 **commonNames** 속성을 사용하여 **certificateCommonNames** 설정을 추가하고 **인증서** 설정을 제거(지문 속성을 사용하여)합니다.
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

## <a name="deploy-the-updated-template"></a>업데이트된 템플릿 배포
변경 후 업데이트된 템플릿을 다시 배포합니다.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>다음 단계
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* [클러스터 인증서 롤오버](service-fabric-cluster-rollover-cert-cn.md) 방법 알아보기
* [클러스터 인증서 업데이트 및 관리](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
