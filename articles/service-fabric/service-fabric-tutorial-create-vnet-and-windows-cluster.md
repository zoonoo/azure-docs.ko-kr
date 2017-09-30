---
title: "Azure에서 Service Fabric 클러스터 만들기 | Microsoft Docs"
description: "템플릿을 사용하여 Azure에서 Windows 클러스터를 만드는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5d56fd468998ee4b1253b47aa133812e0141062b
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-secure-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Azure 가상 네트워크에 보안 Service Fabric Windows 클러스터 배포
이 자습서는 시리즈의 1부입니다. Azure에서 실행되는 Service Fabric 클러스터(Windows)를 만들고 기존 가상 네트워크(VNET) 및 서브넷에 배포하는 방법을 알아봅니다. 작업이 완료되면 응용 프로그램을 배포할 수 있는, 클라우드에서 실행되는 클러스터가 생깁니다.  Linux 클러스터를 만들려면 [템플릿을 사용하여 Azure에서 보안 Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * 템플릿을 사용하여 Azure에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 보안 클러스터 만들기
> * [Service Fabric을 사용하여 API Management 배포](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Service Fabric SDK 및 PowerShell 모듈](service-fabric-get-started.md)을 설치합니다.
- [Azure PowerShell 모듈 버전 4.1 이상](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 설치합니다.

다음 절차에서는 5노드 Service Fabric 클러스터를 만듭니다. 클러스터는 키 자격 증명 모음에 배치된 자체 서명된 인증서에 의해 보호됩니다. 

Azure에서 Service Fabric 클러스터를 실행할 때 발생하는 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.
Service Fabric 클러스터를 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure에 로그인 및 구독 선택
이 가이드에서는 Azure PowerShell을 사용합니다. 새로 PowerShell 세션을 시작하려면 Azure 계정에 로그인한 후 Azure 명령을 실행하기 전에 구독을 선택합니다.
 
다음 스크립트를 실행하여 Azure 계정에 로그인하고 구독을 선택합니다.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
배포에 새 리소스 그룹을 만들고 이름과 위치를 지정합니다.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroup -Name $ResourceGroupName -Location centralus
```

## <a name="deploy-the-network-topology"></a>네트워크 토폴로지를 배포합니다.
다음으로, API Management 및 Service Fabric 클러스터가 배포될 네트워크 토폴로지를 설정합니다. [network.json][network-arm] Resource Manager 템플릿은 VNET(가상 네트워크), Service Fabric에 대한 서브넷 및 NSG(네트워크 보안 그룹), API Management에 대한 서브넷 및 NSG를 만들도록 구성되었습니다. VNET, 서브넷 및 NSG에 대한 자세한 내용은 [여기](../virtual-network/virtual-networks-overview.md)를 참조하세요.

[network.parameters.json][network-parameters-arm] 매개 변수 파일에는 Service Fabric 및 API Management가 배포되는 서브넷 및 NSG의 이름이 포함되어 있습니다.  API Management는 [다음 자습서](service-fabric-tutorial-deploy-api-management.md)에서 배포됩니다. 이 가이드에서는 매개 변수 값을 변경할 필요가 없습니다. 이러한 값은 Service Fabric Resource Manager 템플릿에서 사용됩니다.  여기서 값은 수정하는 경우 이 자습서 및 [API Management 배포 자습서](service-fabric-tutorial-deploy-api-management.md)에서 사용된 다른 Resource Manager 템플릿에서 수정해야 합니다. 

다음 Resource Manager 템플릿 및 매개 변수 파일을 다운로드합니다.
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

다음 PowerShell 명령을 사용하여 네트워크 설정에 대해 Resource Manager 템플릿 및 매개 변수 파일을 배포합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="create-a-key-vault-and-upload-a-certificate"></a>키 자격 증명 모음 만들기 및 인증서 업로드
다음 단계에서는 인증서 보안을 사용하여 보안 클러스터를 만들도록 Service Fabric 클러스터 Resource Manager 템플릿이 구성됩니다. 인증서는 클러스터의 노드 간 통신을 보안하고 Service Fabric 클러스터에 대한 사용자 액세스를 관리하는 데 사용됩니다. 또한 API Management는 이 인증서를 사용하여 서비스 검색을 위해 Service Fabric 명명 서비스에 액세스합니다. 여기에서는 클러스터 보안을 위해 Key Vault에 인증서가 있어야 합니다.

다음 스크립트는 Azure에서 키 자격 증명 모음을 만들고, 자체 서명된 인증서를 만들며, 키 자격 증명 모음에 인증서를 업로드합니다.  기존 인증서를 사용하려면 **$CreateSelfSignedCertificate**를 "$false"로 설정하고 **$ExistingPfxFilePath**에서 위치를 지정합니다.

```powershell
$VaultResourceGroupName = 'ryanwikeyvaultgroup'
$VaultName= 'ryanwikeyvault'
$Location = "westus"
$CertificateName = "ryanwicertificate1"
$Password = 'mypa$$word!'
$DnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$OutputPath = "C:\MyCertificates" # location where you want the .PFX to be stored
$CreateSelfSignedCertificate = $true
$ExistingPfxFilePath = 'C:\MyCertificates\ryanwicertificate1.pfx'

$ErrorActionPreference = 'Stop'

Write-Host "Switching context to SubscriptionId $SubscriptionId"
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# New-AzureRmResourceGroup is idempotent as long as the location matches
Write-Host "Ensuring ResourceGroup $VaultResourceGroupName in $Location"
New-AzureRmResourceGroup -Name $VaultResourceGroupName -Location $Location -Force | Out-Null
$resourceId = $null

try
{
    $existingKeyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName
    $resourceId = $existingKeyVault.ResourceId

    Write-Host "Using existing vault $VaultName in $($existingKeyVault.Location)"
}
catch
{
}

if(!$existingKeyVault)
{
    Write-Host "Creating new vault $VaultName in $location"
    $newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName -Location $Location -EnabledForDeployment
    $resourceId = $newKeyVault.ResourceId
}

if($CreateSelfSignedCertificate)
{
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force

    $NewPfxFilePath = Join-Path $OutputPath $($CertificateName+".pfx")

    Write-Host "Creating new self signed certificate at $NewPfxFilePath"
    
    ## Changes to PSPKI version 3.5.2 New-SelfSignedCertificate replaced by New-SelfSignedCertificateEx
    $PspkiVersion = (Get-Module PSPKI).Version
    if($PSPKIVersion.Major -ieq 3 -And $PspkiVersion.Minor -ieq 2 -And $PspkiVersion.Build -ieq 5) {
        New-SelfsignedCertificateEx -Subject "CN=$DnsName" -EKU "Server Authentication", "Client authentication" -KeyUsage "KeyEncipherment, DigitalSignature" -Path $NewPfxFilePath -Password $securePassword -Exportable
    }
    else {
        New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName $DnsName | Export-PfxCertificate -FilePath $NewPfxFilePath -Password $securePassword | Out-Null
    }

    $ExistingPfxFilePath = $NewPfxFilePath
}

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $Password

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $Password
   } | ConvertTo-Json

    $contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
    $content = [System.Convert]::ToBase64String($contentbytes)

    $secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

Write-Host "Writing secret to $CertificateName in vault $VaultName"
$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertificateName -SecretValue $secretValue

$output = @{};
$output.SourceVault = $resourceId;
$output.CertificateURL = $secret.Id;
$output.CertificateThumbprint = $cert.Thumbprint;

Write-Host "Source vault: " $output.SourceVault
Write-Host "Certificate URL: " $output.CertificateURL
Write-Host "Certificate Thumbprint: " $output.CertificateThumbprint
```

## <a name="deploy-the-service-fabric-cluster"></a>Service Fabric 클러스터 배포
네트워크 리소스에서 배포가 완료되고 키 자격 증명 모음에 인증서를 업로드했다면 다음 단계로 Service Fabric 클러스터용으로 지정된 서브넷 및 NSG의 VNET에 Service Fabric 클러스터를 배포합니다. 이 자습서에서는 Service Fabric Resource Manager 템플릿이 이전 단계에서 설정한 VNET, 서브넷 및 NSG의 이름을 사용하도록 미리 구성되어 있습니다.

다음 Resource Manager 템플릿 및 매개 변수 파일을 다운로드합니다.
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

클러스터 인증을 위한 [Key Vault 정보](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault)를 포함하여 배포를 위해 `cluster.parameters.json` 파일에서 빈 매개 변수를 채우세요.

다음 PowerShell 명령을 통해 Resource Manager 템플릿 및 매개 변수 파일을 배포하여 Service Fabric 클러스터를 만듭니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
```

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>인증서 수정 및 Service Fabric Explorer에 액세스 

1. 인증서를 두 번 클릭하여 인증서 가져오기 마법사를 엽니다.

2. 기본 설정을 사용하지만 **이 키를 내보낼 수 있도록 표시** 확인란을 **개인 키 보호** 단계에서 선택해야 합니다. 이 자습서의 뒷부분에 나오는 Azure Container Registry를 Service Fabric 클러스터 인증으로 구성할 때 Visual Studio에서 인증서를 내보내야 합니다.

3. 이제 브라우저에서 Service Fabric Explorer를 열 수 있습니다. 이렇게 하려면 웹 브라우저를 사용하여 클러스터에 대한 **ManagementEndpoint** URL로 이동하고 컴퓨터에 저장된 인증서를 선택합니다.

>[!NOTE]
>Service Fabric Explorer를 열면 인증서 오류가 표시되는데 이는 자체 서명된 인증서를 사용하고 있기 때문입니다. Edge에서는 *세부 정보*를 클릭한 다음 *웹 페이지로 이동* 링크를 클릭합니다. Chrome에서는 *고급* 및 *진행* 링크를 클릭합니다.

>[!NOTE]
>클러스터 작성이 실패한 경우 이미 배포된 리소스를 업데이트하는 명령을 언제든지 다시 실행할 수 있습니다. 실패한 배포의 일부로 인증서가 만들어진 경우 새 인증서가 생성됩니다. 클러스터 만들기 문제를 해결하려면 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.

## <a name="connect-to-the-secure-cluster"></a>보안 클러스터에 연결
Service Fabric SDK가 설치된 Service Fabric PowerShell 모듈을 사용하여 클러스터에 연결합니다.  먼저 사용자 컴퓨터에서 현재 사용자의 개인(내) 저장소에 인증서를 설치합니다.  다음 PowerShell 명령을 실행합니다.

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

이제 보안 클러스터에 연결할 준비가 되었습니다.

**Service Fabric** PowerShell 모듈은 Service Fabric 클러스터, 응용 프로그램 및 서비스를 관리하기 위한 많은 cmdlet을 제공합니다.  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet을 사용하여 보안 클러스터에 연결합니다. 인증서 지문 및 연결 끝점 세부 정보는 이전 단계의 출력에 있습니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet을 사용하여 사용자가 연결되어 있고 클러스터가 정상 상태인지 확인합니다.

```powershell
Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster health
```

## <a name="clean-up-resources"></a>리소스 정리

클러스터는 클러스터 리소스 외에도 다른 Azure 리소스로 이루어져 있습니다. 클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다.

Azure에 로그인하고 클러스터를 제거할 구독 ID를 선택합니다.  [Azure Portal](http://portal.azure.com)에 로그인하여 구독 ID를 찾을 수 있습니다. [Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 VNET 만들기
> * 키 자격 증명 모음 만들기 및 인증서 업로드
> * 템플릿을 사용하여 Azure에서 보안 Service Fabric 클러스터 만들기
> * X.509 인증서를 사용하여 클러스터 보호
> * PowerShell을 사용하여 클러스터에 연결
> * 클러스터 제거

이제 다음 자습서로 이동하여 기존 응용 프로그램을 배포하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [API Management 배포](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

