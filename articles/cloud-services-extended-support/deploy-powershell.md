---
title: Azure Cloud Service(추가 지원) 배포 - PowerShell
description: PowerShell을 사용하여 Azure Cloud Service(추가 지원)를 배포합니다.
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a7e4e76aa0619d78a91d766a9a43c0b1a02a48d3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717390"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Azure PowerShell을 사용하여 Cloud Service(추가 지원) 배포

이 문서에서는 `Az.CloudService` PowerShell 모듈을 사용하여 Azure에서 여러 역할(WebRole 및 WorkerRole)과 원격 데스크톱 확장이 있는 Cloud Services(추가 지원)를 배포하는 방법에 대해 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다. 

## <a name="deploy-a-cloud-services-extended-support"></a>Cloud Services(추가 지원) 배포
1. Az.CloudService PowerShell 모듈을 설치합니다.  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. 새 리소스 그룹 만들기 기존 리소스 그룹을 사용하는 경우 이 단계는 선택 사항입니다.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Cloud Service 패키지(.cspkg) 및 서비스 구성(.cscfg) 파일을 저장하는 데 사용할 스토리지 계정 및 컨테이너를 만듭니다. 고유한 이름을 스토리지 계정 이름에 사용해야 합니다. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Cloud Service 패키지(cspkg)를 스토리지 계정에 업로드합니다.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  클라우드 서비스 구성(cscfg)을 스토리지 계정에 업로드합니다. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. 가상 네트워크와 서브넷을 만듭니다. 기존 네트워크 및 서브넷을 사용하는 경우 이 단계는 선택 사항입니다. 다음 예제에서는 단일 가상 네트워크 및 서브넷을 두 클라우드 서비스 역할(WebRole 및 WorkerRole)에 사용합니다. 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. 공용 IP 주소를 만들고 공용 IP 주소의 DNS 레이블 속성을 설정합니다. Cloud Services(확장 지원)는 [기본](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU 공용 IP 주소만 지원합니다. 표준 SKU 공용 IP는 Cloud Services에서 작동하지 않습니다.
고정 IP를 사용하는 경우 서비스 구성(.cscfg) 파일에서 예약된 IP로 참조해야 합니다. 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. 네트워크 프로필 개체를 만들고, 공용 IP 주소를 부하 분산 장치의 프런트 엔드에 연결합니다. Azure 플랫폼은 클라우드 서비스 리소스와 동일한 구독에서 '클래식' SKU 부하 분산 장치 리소스를 자동으로 만듭니다. 부하 분산 장치 리소스는 ARM의 읽기 전용 리소스입니다. 리소스에 대한 모든 업데이트는 클라우드 서비스 배포 파일(.cscfg & .csdef)을 통해서만 지원됩니다.

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Key Vault를 만듭니다. 이 Key Vault는 클라우드 서비스(추가 지원) 역할에 연결된 인증서를 저장하는 데 사용됩니다. Key Vault는 클라우드 서비스와 동일한 지역 및 구독에 있어야 하며 고유한 이름을 사용해야 합니다. 자세한 내용은 [Azure Cloud Services(추가 지원)에서 인증서 사용](certificates-and-key-vault.md)을 참조하세요.

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Key Vault 액세스 정책을 업데이트하고, 인증서 권한을 사용자 계정에 부여합니다. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    또는 ObjectId(`Get-AzADUser`를 실행하여 가져올 수 있음)를 통해 액세스 정책을 설정합니다. 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. 다음 예제에서는 자체 서명된 인증서를 Key Vault에 추가합니다. 클라우드 서비스 역할에 배포하려면 인증서 지문을 Cloud Service 구성(.cscfg) 파일에 추가해야 합니다. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. OS 프로필 메모리 내 개체를 만듭니다. OS 프로필은 클라우드 서비스 역할에 연결된 인증서를 지정합니다. 이는 이전 단계에서 만든 것과 동일한 인증서입니다. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. 역할 프로필 메모리 내 개체를 만듭니다. 역할 프로필은 이름, 용량 및 계층과 같은 역할 SKU 관련 속성을 정의합니다. 다음 예제에서는 frontendRole 및 backendRole의 두 가지 역할을 정의했습니다. 역할 프로필 정보는 구성(cscfg) 파일 및 서비스 정의(csdef) 파일에 정의된 역할 구성과 일치해야 합니다. 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (선택 사항) 클라우드 서비스에 추가하려는 확장 프로필 메모리 내 개체를 만듭니다. 다음 예제에서는 RDP 확장을 추가합니다. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    configFile에는 PublicConfig 태그만 있어야 하며, 다음과 같은 네임스페이스를 포함해야 합니다.
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (선택 사항) 태그를 클라우드 서비스에 추가하려는 PowerShell 해시 테이블로 정의합니다. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. 프로필 개체 및 SAS URL을 사용하여 Cloud Service 배포를 만듭니다.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.md)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support)를 방문합니다.
