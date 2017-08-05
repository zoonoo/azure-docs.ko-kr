---
title: "템플릿에서 Azure Service Fabric 클러스터 만들기 | Microsoft Docs"
description: "이 문서에서는 클라이언트 인증에 Azure Resource Manager, Azure Key Vault, Azure Active Directory(Azure AD)를 사용하여 Azure에 보안 Service Fabric 클러스터를 설정하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: 8d876a0f2168ee9375a3905d5d5a562ab1194cf3
ms.openlocfilehash: 9159f40fed17e52e6576efa1ea7e8a2dee98728e
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017

---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기
> [!div class="op_single_selector"]
> * [Azure 리소스 관리자](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Azure Resource Manager를 사용하여 Azure에 보안 Azure Service Fabric 클러스터를 설정하는 단계를 안내하는 단계별 가이드입니다. 이 문서는 깁니다. 그렇지만 아직 이 내용에 익숙하지 않다면 각 단계를 세심히 따라보시기 바랍니다. 

이 가이드에서는 다음 절차를 다룹니다.

* 클러스터 및 응용 프로그램 보안을 위한 인증서 업로드를 위해 Azure Key Vault 설정
* Azure Resource Manager를 사용하여 Azure에 보안 클러스터 만들기
* 클러스터 관리를 위해 Azure Active Directory(Azure AD)를 사용하여 사용자 인증

보안 클러스터는 관리 작업에 대한 무단 액세스를 차단합니다. 여기에는 응용 프로그램, 서비스 및 포함된 데이터의 배포, 업그레이드 및 삭제가 포함됩니다. 비보안 클러스터란 언제라도 누구든지 연결하여 관리 작업을 수행할 수 있는 클러스터입니다. 비보안 클러스터를 만들 수도 있지만 처음부터 보안 클러스터를 만드는 것이 좋습니다. 비보안 클러스터는 나중에 보안 클러스터로 만들 수 없으므로 새 클러스터를 만들어야 합니다.

Linux나 Windows 클러스터 모두 보안 클러스터를 만드는 개념은 같습니다. 자세한 내용 및 보안 Linux 클러스터 만들기를 위한 도우미 스크립트는 [Linux에서 보안 클러스터 만들기](#secure-linux-clusters)를 참조하세요.

## <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인
이 가이드에서는 [Azure PowerShell][azure-powershell]을 사용합니다. 새로 PowerShell 세션을 시작하려면 Azure 계정에 로그인한 후 Azure 명령을 실행하기 전에 구독을 선택합니다.

Azure 계정 로그인:

```powershell
Login-AzureRmAccount
```

구독을 선택합니다.

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Key Vault 설정
이 섹션에서는 Azure에서 Service Fabric 클러스터에 대해서와 Service Fabric 응용 프로그램에 대해서 Key Vault를 만드는 단계를 살펴봅니다. Azure Key Vault에 대한 완전한 가이드는 [Key Vault 시작 가이드][key-vault-get-started]를 참조하세요.

서비스 패브릭은 클러스터에 보안 적용을 하고 응용 프로그램 보안 기능을 제공하기 위해 X.509 인증서를 사용합니다. Key Vault는 Azure에서 Service Fabric 클러스터에 대한 인증서를 관리하는 데 사용됩니다. 클러스터를 Azure에 배포할 때 Service Fabric 클러스터 생성을 담당하는 Azure 리소스 공급자는 Key Vault에서 인증서를 가져와 클러스터 VM에 설치합니다.

다음 다이어그램은 Azure Key Vault, Service Fabric 클러스터 및 클러스터를 만들 때 Key Vault에 저장된 인증서를 사용하는 Azure 리소스 공급자 간의 관계를 보여 줍니다.

![인증서 설치 다이어그램][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
첫 번째 단계는 Key Vault 전용 리소스 그룹을 만드는 것입니다. Key Vault를 자체 리소스 그룹에 배치하는 것이 좋습니다. 이렇게 하면 키 및 암호는 유실하지 않고 Service Fabric 클러스터가 있는 리소스 그룹과 같은 계산 및 저장소 리소스 그룹을 제거할 수 있습니다. 사용자 Key Vault를 포함하는 리소스 그룹은 해당 그룹을 사용하는 클러스터와 _동일한 지역에 있어야_ 합니다.

여러 지역에 클러스터를 배포하려는 경우 리소스 그룹과 Key Vault를 해당 항목이 속한 지역을 표시하는 방식으로 명명하는 것이 좋습니다.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
출력은 다음과 같습니다.

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>새 리소스 그룹에 Key Vault 만들기
연산 리소스 공급자가 인증서를 가져와 가상 컴퓨터 인스턴스에 설치할 수 있도록 Key Vault를 _배포에 대해 사용하도록 설정해야 합니다_.

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

출력은 다음과 같습니다.

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>기존 Key Vault 사용

기존 Key Vault를 사용하려면 연산 리소스 공급자가 인증서를 가져와 클러스터 노드에 설치할 수 있도록 Key Vault를 _배포에 대해 사용하도록 설정해야 합니다_.

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Key Vault에 인증서 추가

인증서는 서비스 패브릭에서 클러스터 및 해당 응용 프로그램의 다양한 측면을 보호하기 위해 인증 및 암호화를 제공하는 데 사용됩니다. Service Fabric에서 인증서가 사용되는 방식에 대한 자세한 내용은 [Service Fabric 클러스터 보안 시나리오][service-fabric-cluster-security]를 참조하세요.

### <a name="cluster-and-server-certificate-required"></a>클러스터 및 서버 인증서(필수)
이 인증서는 클러스터를 보호하고 무단 액세스를 방지하기 위해 필요합니다. 다음 두 방법으로 클러스터 보안을 제공합니다.

* 클러스터 인증: 클러스터 페더레이션을 위해 노드 간 통신을 인증합니다. 이 인증서로 자신의 신분을 증명할 수 있는 노드만 클러스터에 가입할 수 있습니다.
* 서버 인증: 관리 클라이언트에 클러스터 관리 끝점을 인증하여 관리 클라이언트기 실제 클러스터와 통신하는지 알 수 있도록 합니다. 이 인증은 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer용 SSL도 제공합니다.

이를 위해 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 키 교환을 위해 인증서를 만들어야 합니다. 이 인증서는 개인 정보 교환(.pfx) 파일로 내보낼 수 있습니다.
* 인증서의 주체 이름은 Service Fabric 클러스터 액세스에 사용되는 도메인과 일치해야 합니다. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이렇게 일치해야 합니다. .cloudapp.azure.com 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다.  클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

### <a name="application-certificates-optional"></a>응용 프로그램 인증서(선택 사항)
응용 프로그램 보안을 위해 클러스터에 제한 없는 수의 인증서를 추가로 설치할 수 있습니다. 클러스터를 만들기 전에, 다음과 같이 노드에 인증서를 설치하도록 요구하는 응용 프로그램 보안 시나리오를 고려해 보세요.

* 응용 프로그램 구성 값의 암호화 및 암호 해독
* 복제 중에 노드 간 데이터 암호화

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Azure 리소스 공급자 사용을 위한 인증서 서식 지정
Key Vault를 통해 개인 키 파일(.pfx)을 직접 추가하여 사용할 수 있습니다. 그러나 Azure 계산 리소스 공급자에는 특수 JSON(JavaScript Object Notation) 형식으로 저장된 키가 필요합니다. 이 형식에는 기본 64 인코딩 문자열형태의 .pfx 파일과 개인 키 암호가 포함됩니다. 이러한 요구를 수용하기 위해 키를 JSON 문자열에 배치한 후 Key Vault에 “암호”로 저장해야 합니다.

이 프로세스를 보다 쉽게 수행할 수 있도록 하기 위해 PowerShell 모듈이 [GitHub에서 사용할 수 있게 제공됩니다][service-fabric-rp-helpers]. 모듈을 사용하려면 다음을 수행합니다.

1. 리포지토리의 전체 콘텐츠를 로컬 디렉터리에 다운로드합니다.
2. 로컬 디렉터리로 이동합니다.
2. PowerShell 창에서 ServiceFabricRPHelpers 모듈을 가져옵니다.

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

이 PowerShell 모듈의 `Invoke-AddCertToKeyVault` 명령은 자동으로 인증서 개인 키 서식을 JSON 문자열에 지정하고 Key Vault에 업로드합니다. 이 명령을 사용하여 클러스터 인증서 및 추가 응용 프로그램 인증서를 Key Vault에 추가합니다. 클러스터에 설치하려는 모든 추가 인증서에 대해 이 단계를 반복합니다.

#### <a name="uploading-an-existing-certificate"></a>기존 인증서 업로드

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

아래와 같은 오류가 나타나면 보통은 리소스 URL 충돌을 의미합니다. 이 충돌을 해결하려면 Key Vault 이름을 변경합니다.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

충돌이 해결된 후의 출력은 다음과 같습니다.

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>보안 Service Fabric 클러스터를 설정하고 응용 프로그램 보안에 사용할 수 있는 모든 응용 프로그램 인증서를 가져오려면 이전 세 문자열, CertificateThumbprint, SourceVault 및 CertificateURL이 필요합니다. 이 문자열을 저장하지 않은 경우 나중에 Key Vault를 쿼리하여 검색하기 어려울 수 있습니다.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>자체 서명된 인증서를 만들어 Key Vault에 업로드

이미 인증서를 Key Vault에 업로드한 경우 이 단계를 건너뜁니다. 이 단계는 새로운 자체 서명 인증서를 생성하여 Key Vault에 업로드하기 위한 것입니다. 다음 스크립트의 매개 변수를 변경하여 실행한 다음에는 인증서 암호를 입력하라는 메시지가 나타납니다.  

```powershell

$ResourceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResourceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

아래와 같은 오류가 나타나면 보통은 리소스 URL 충돌을 의미합니다. 이 충돌을 해결하려면 Key Vault 이름, RG 이름 등을 변경합니다.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

충돌이 해결된 후의 출력은 다음과 같습니다.

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>보안 Service Fabric 클러스터를 설정하고 응용 프로그램 보안에 사용할 수 있는 모든 응용 프로그램 인증서를 가져오려면 이전 세 문자열, CertificateThumbprint, SourceVault 및 CertificateURL이 필요합니다. 이 문자열을 저장하지 않은 경우 나중에 Key Vault를 쿼리하여 검색하기 어려울 수 있습니다.

 이 시점에 다음과 같은 요소가 있어야 합니다.

* Key Vault 리소스 그룹
* Key Vault 및 해당 URL(이전 PowerShell 출력의 SourceVault)
* 클러스터 서버 인증 인증서 및 Key Vault의 해당 URL
* 응용 프로그램 인증서 및 Key Vault의 해당 URL


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>클라이언트 인증에 대한 Azure Active Directory 설정

조직(테넌트)에서는 Azure AD를 사용하여 응용 프로그램에 대한 사용자 액세스를 관리할 수 있습니다. 응용 프로그램은 웹 기반 로그인 UI를 갖는 항목과 네이티브 클라이언트 환경을 갖는 항목으로 나뉩니다. 이 문서에서는 이미 테넌트를 만들었다고 가정합니다. 그러지 않은 경우 [Azure Active Directory 테넌트를 얻는 방법][active-directory-howto-tenant]을 참조하세요.

Service Fabric 클러스터는 웹 기반 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 및 [Visual Studio][service-fabric-manage-application-in-visual-studio]를 포함하여 관리 기능에 대한 여러 진입점을 제공합니다. 결과적으로 두 개의 Azure AD 응용 프로그램(웹 응용 프로그램과 네이티브 응용 프로그램)을 만들어 클러스터에 대한 액세스를 제어합니다.

Service Fabric 클러스터로 Azure AD를 구성하는 데 포함되는 일부 단계를 단순화하기 위해 Windows PowerShell 스크립트 집합을 만들었습니다.

> [!NOTE]
> 클러스터를 만들기 전에 다음 단계를 완료해야 합니다. 스크립트는 클러스터 이름과 끈점을 예상하므로 이 값을 계획해야 하며, 이 값은 이미 만든 값이 아닙니다.

1. 컴퓨터에 [스크립트를 다운로드][sf-aad-ps-script-download]합니다.
2. zip 파일을 마우스 오른쪽 단추로 클릭하고 **속성**, **차단 해제** 확인란을 차례로 선택한 다음 **적용**을 클릭합니다.
3. zip 파일의 압축을 풉니다.
4. `SetupApplications.ps1`을 실행하고 TenantId, ClusterName 및 WebApplicationReplyUrl을 매개 변수로 제공합니다. 예:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    PowerShell 명령 `Get-AzureSubscription`을 실행하여 TenantId를 찾을 수 있습니다. 이 명령을 실행하면 모든 구독에 대한 TenantId가 표시됩니다.

    ClusterName은 스크립트로 만든 Azure AD 응용 프로그램에 접두사를 지정하는 데 사용됩니다. 실제 클러스터 이름과 정확히 일치할 필요는 없습니다. 단순히 Azure AD 아티팩트를, 함께 사용할 Service Fabric 패브릭 클러스터에 쉽게 매핑하기 위한 것입니다.

    WebApplicationReplyUrl은 로그인을 마친 후 Azure AD가 사용자를 돌려보낼 기본 끝점입니다. 이 끝점을 기본적으로 다음과 같은 클러스터에 대한 Service Fabric Explorer 끝점으로 설정합니다.

    https://&lt;cluster_domain&gt;:19080/Explorer

    Azure AD 테넌트에 대한 관리자 권한이 있는 계정으로 로그인하라는 메시지가 표시됩니다. 로그인한 후에는 스크립트가 Service Fabric 클러스터를 나타내는 웹 및 네이티브 응용 프로그램을 만듭니다. [Azure 클래식 포털][azure-classic-portal]에서 테넌트의 응용 프로그램을 보면 두 개의 새 항목이 표시됩니다.

   * *ClusterName*\_클러스터
   * *ClusterName*\_클라이언트

   다음 섹션에서 클러스터를 만들 때 스크립트는 Azure Resource Manager 템플릿에서 필요한 JSON을 인쇄하므로 PowerShell 창을 계속 열어 두는 것이 좋습니다.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>서비스 패브릭 클러스터 Resource Manager 템플릿 만들기
이 섹션에서는 이전 PowerShell 명령의 출력이 Service Fabric Cluster Resource Manager 템플릿에서 사용됩니다.

샘플 Resource Manager 템플릿은 [GitHub의 Azure 빠른 시작 템플릿 갤러리][azure-quickstart-templates]에 제공됩니다. 이러한 템플릿은 클러스터 템플릿의 시작점으로 사용할 수 있습니다.

### <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기
이 가이드에서는 [5 노드 보안 클러스터][service-fabric-secure-cluster-5-node-1-nodetype] 예제 템플릿과 템플릿 매개 변수를 사용합니다. `azuredeploy.json`와 `azuredeploy.parameters.json`을 컴퓨터에 다운로드하고 선호하는 텍스트 편집기로 두 파일을 엽니다.

### <a name="add-certificates"></a>인증서 추가
인증서 키를 포함하는 Key Vault 참조하여 Cluster Resource Manager 템플릿에 인증서를 추가합니다. Resource Manager 템플릿 파일에 Key Vault 값을 두는 것이 좋습니다. 이렇게 하면 Resource Manager 템플릿을 계속 재사용할 수 있고 배포에 특정한 값이 없게 됩니다.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>가상 컴퓨터 크기 집합 osProfile에 모든 인증서 추가
클러스터에 설치된 모든 인증서는 크기 집합 리소스(Microsoft.Compute/virtualMachineScaleSets)의 osProfile 섹션에 구성되어야 합니다. 이 작업은 리소스 공급자에게 인증서를 VM에 설치하도록 지시합니다. 이 설치에는 클러스터 인증서와, 응용 프로그램에 사용하려는 모든 응용 프로그램 보안 인증서가 포함됩니다.

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Service Fabric 클러스터 인증서 구성
또한 클러스터 인증 인증서는 서비스 패브릭 클러스터 리소스(Microsoft.ServiceFabric/clusters)와, 가상 컴퓨터 크기 집합 리소스의 가상 컴퓨터 크기 집합에 대해 Service Fabric 확장에서 모두 구성되어야 합니다. 이렇게 해야 Service Fabric 리소스 공급자는 클러스터 인증에 대한 사용 및 관리 끝점에 대한 서버 인증을 위해 그것을 구성할 수 있습니다.

##### <a name="virtual-machine-scale-set-resource"></a>가상 컴퓨터 크기 집합 리소스:
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>서비스 패브릭 리소스:
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>Azure AD 구성 삽입
이전에 만든 Azure AD 구성을 Resource Manager 템플릿에 직접 삽입할 수 있습니다. 그러나 Resource Manager 템플릿을 계속 재사용하고 배포에 특정한 값이 없도록 하기 위해 먼저 값을 매개 변수 파일에 추출하는 것이 좋습니다.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a "configure-arm" ></a>Resource Manager 템플릿 매개 변수 구성
<!--- Loc Comment: It seems that <a "configure-arm" > must be replaced with <a name="configure-arm"></a> since the link seems not to be redirecting correctly --->
마지막으로, Key Vault 및 Azure AD PowerShell 명령의 출력 값을 매개 변수 파일을 채우는 데 사용합니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
이 시점에 다음과 같은 요소가 있어야 합니다.

* Key Vault 리소스 그룹
  * 주요 자격 증명 모음
  * 클러스터 서버 인증 인증서
  * 데이터 암호화 인증서
* Azure Active Directory 테넌트
  * 웹 기반 관리 및 Service Fabric Explorer에 대한 Azure AD 응용 프로그램
  * 네이티브 클라이언트 관리에 대한 Azure AD 응용 프로그램
  * 사용자 및 할당된 역할
* 서비스 패브릭 클러스터 Resource Manager 템플릿
  * Key Vault를 통해 구성된 인증서
  * 구성된 Azure Active Directory

다음 다이어그램은 Key Vault 및 Azure AD 구성이 Resource Manager 템플릿 어디에 적합한지 보여 줍니다.

![리소스 관리자 종속성 맵][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>클러스터 만들기
이제 [Azure 리소스 템플릿 배포][resource-group-template-deploy]를 사용하여 클러스터를 만들 준비가 되었습니다.

#### <a name="test-it"></a>테스트
매개 변수 파일로 Resource Manager 템플릿을 테스트하려면 다음 PowerShell 명령을 사용합니다.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>배포
Resource Manager 템플릿 테스트를 통과했다면 매개 변수 파일로 Resource Manager 템플릿을 배포하기 위해 다음 PowerShell 명령을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>역할에 사용자 할당
클러스터를 나타내는 응용 프로그램을 만들었으면 사용자를 Service Fabric에서 지원하는 역할(읽기 전용 및 관리자)에 할당합니다. [Azure 클래식 포털][azure-classic-portal]을 사용하여 역할을 할당할 수 있습니다.

1. Azure Portal에서 테넌트로 이동한 다음 **응용 프로그램**을 선택합니다.
2. 이름이 `myTestCluster_Cluster` 같은 웹 응용 프로그램을 선택합니다.
3. **사용자** 탭을 클릭합니다.
4. 할당할 사용자를 선택하고 화면 아래쪽에 있는 **할당** 단추를 클릭합니다.

    ![역할에 사용자 할당 단추][assign-users-to-roles-button]
5. 사용자에게 할당할 역할을 선택합니다.

    !["사용자 지정" 대화 상자][assign-users-to-roles-dialog]

> [!NOTE]
> 서비스 패브릭의 역할에 대한 자세한 내용은 [서비스 패브릭 클라이언트의 역할 기반 액세스 제어](service-fabric-cluster-security-roles.md)를 참조하세요.
>
>

 <a name="secure-linux-clusters"></a>
 <!--- Loc Comment: It seems that letter S in cluster was missing, which caused the wrong redirection of the link --->

## <a name="create-secure-clusters-on-linux"></a>Linux에서 보안 클러스터 만들기
작업 편의를 위해 [도우미 스크립트](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux)가 제공됩니다. 이 도우미 스크립트를 사용하기 전에 먼저 Azure CLI(명령줄 인터페이스)를 설치했으며 해당 경로에 있는지 확인합니다. 다운로드한 후 `chmod +x cert_helper.py` 를 실행하여 스크립트에 실행할 권한이 있는지 확인합니다. 첫 번째 단계에서는 `azure login` 명령으로 CLI를 사용하여 Azure 계정에 로그인합니다. Azure 계정에 로그인한 후 다음 명령에 표시된 것처럼 CA 서명된 인증서로 도우미 스크립트를 사용합니다.

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

-ifile 매개 변수는 입력으로 .pfx 파일 또는 .pem 파일과 인증서 형식(pfx나 pem 또는 자체 서명 인증서의 경우 ss)을 받습니다.
-h 매개 변수는 도움말 텍스트를 인쇄합니다.


이 명령은 출력으로 다음 3개의 문자열을 반환합니다.

* SourceVaultID - 사용자를 위해 생성된 새로운 KeyVault ResourceGroup의 ID 
* 인증서에 액세스하기 위한 CertificateUrl
* 인증에 사용되는 CertificateThumbprint 

다음 예제에서는 명령을 사용하는 방법을 보여 줍니다.

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
위의 명령을 실행하면 다음과 같이 3가지 문자열이 표시됩니다.

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

인증서의 주체 이름은 Service Fabric 클러스터 액세스에 사용되는 도메인과 일치해야 합니다. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이렇게 일치해야 합니다. `.cloudapp.azure.com` 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

이 주체 이름은 [Resource Manager 템플릿 매개 변수 구성](#configure-arm)에 설명된 대로 보안 Service Fabric 클러스터(Azure AD 없음)를 만드는 데 필요한 항목입니다. [클러스터에 대한 클라이언트 액세스 인증](service-fabric-connect-to-secure-cluster.md)을 위한 다음 지침을 통해 보안 클러스터에 연결할 수 있습니다. Linux 미리 보기 클러스터에서는 Azure AD 인증을 지원하지 않습니다. [사용자에게 역할 할당](#assign-roles) 섹션에 설명된 대로 관리 및 클라이언트 역할을 할당할 수 있습니다. Linux 미리 보기 클러스터에 대해 관리자 및 클라이언트 역할을 지정할 때는 인증에 대한 인증서 지문을 제공해야 합니다. 이 미리 보기 릴리스에서는 체인 유효성 검사나 해지가 수행되지 않으므로 주체 이름은 제공하지 않습니다.

자체 서명 인증서를 테스트에 사용하려는 경우 같은 스크립트를 사용하여 생성할 수 있습니다. 그런 다음 인증서 경로와 인증서 이름 대신 `ss` 플래그를 제공하여 Key Vault에 해당 인증서를 업로드할 수 있습니다. 예를 들어 자체 서명된 인증서를 만들고 업로드하는 다음 명령을 참조하세요.

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
이 명령은 동일한 3개의 문자열 SourceVault, CertificateUrl 및 CertificateThumbprint를 반환합니다. 그런 다음 문자열을 사용하여 보안 Linux 클러스터와, 자체 서명 인증서가 있는 위치를 모두 만들 수 있습니다. 클러스터에 연결하려면 자체 서명 인증서가 필요합니다. [클러스터에 대한 클라이언트 액세스 인증](service-fabric-connect-to-secure-cluster.md)을 위한 다음 지침을 통해 보안 클러스터에 연결할 수 있습니다.

인증서의 주체 이름은 Service Fabric 클러스터 액세스에 사용되는 도메인과 일치해야 합니다. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이렇게 일치해야 합니다. `.cloudapp.azure.com` 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

[Azure Portal에서 클러스터 만들기](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal) 섹션에서 설명한 대로 Azure Portal에서 도우미 스크립트를 통해 매개 변수를 입력할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 Azure Active Directory를 사용하여 관리 인증을 제공하는 보안 클러스터가 생겨났습니다. 다음으로, [클러스터에 연결](service-fabric-connect-to-secure-cluster.md)하고 [응용 프로그램 암호를 관리](service-fabric-application-secret-management.md)하는 방법을 알아봅니다.

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>클라이언트 인증에 대한 Azure Active Directory 설정 문제 해결
클라이언트 인증을 위한 Azure AD를 설정하는 동안 문제가 발생할 경우 이 섹션에서 가능한 해결 방법을 찾아봅니다.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer에 인증서를 선택하라는 메시지가 표시 
#### <a name="problem"></a>문제
Service Fabric Explorer에서 Azure AD에 로그인한 후 브라우저가 홈 페이지로 돌아가지만 인증서를 선택하라는 메시지가 표시됩니다.

![SFX 인증서 선택 대화 상자][sfx-select-certificate-dialog]

#### <a name="reason"></a>이유
Azure AD 클러스터 응용 프로그램에서 사용자에게 역할이 할당되지 않았습니다. 이 때문에 Service Fabric 클러스터에서 Azure AD 인증이 실패합니다. Service Fabric Explorer는 인증서 인증으로 대체됩니다.

#### <a name="solution"></a>해결 방법
Azure AD 설정 지침을 따르고 사용자 역할을 할당합니다. 또한 `SetupApplications.ps1`에서처럼 “앱에 액세스하려면 사용자 할당 필요”를 살펴보는 것이 좋습니다.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>“지정된 자격 증명이 올바르지 않다”는 오류가 표시되면서 PowerShell 연결 실패
#### <a name="problem"></a>문제
PowerShell을 사용하여 “AzureActiveDirectory”보안 모드로 클러스터에 연결할 때, Azure AD 로그인 후 연결이 실패하고 “지정된 자격 증명이 올바르지 않다"는 오류가 표시됨

#### <a name="solution"></a>해결 방법
이 솔루션은 이전과 동일합니다.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>로그인할 때 Service Fabric Explorer가 실패를 반환함: "AADSTS50011"
#### <a name="problem"></a>문제
Service Fabric Explorer에서 Azure AD에 로그인할 때 페이지가 "AADSTS50011: 회신 주소 &lt;url&gt;이 응용 프로그램 &lt;guid&gt;에 대해 구성된 회신 주소와 일치하지 않습니다.”라는 실패를 반환합니다.

![SFX 회신 주소가 일치하지 않습니다.][sfx-reply-address-not-match]

#### <a name="reason"></a>이유
Service Fabric Explorer를 나타내는 클러스터(웹) 응용 프로그램이 Azure AD에 대해 인증을 시도하며, 해당 요청의 일부로 리디렉션 반환 URL을 제공합니다. 그렇지만 Azure AD 응용 프로그램 **REPLY URL** 목록에 표시되지 않습니다.

#### <a name="solution"></a>해결 방법
클러스터(웹) 응용 프로그램의 **Configure** 탭에서 **REPLY URL** 목록에 Service Fabric Explorer URL을 추가하거나 목록의 항목 중 하나를 대체합니다. 마친 후 변경 사항을 저장합니다.

![웹 응용 프로그램 회신 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>PowerShell 통해 Azure AD 인증을 사용하여 클러스터 연결
Service Fabric 클러스터에 연결하려면 다음 PowerShell 명령 예제를 사용합니다.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Connect-ServiceFabricCluster cmdlet에 대해 알아보려면 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)를 참조하세요.

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>여러 클러스터에서 동일한 Azure AD테넌트를 다시 사용할 수 있나요?
예. 그렇지만 Service Fabric Explorer의 URL을 클러스터(웹) 응용 프로그램에 추가해야 합니다. 그러지 않으면 Service Fabric Explorer가 작동하지 않습니다.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Azure AD가 사용되도록 설정된 경우에도 서버 인증서가 계속 필요한 이유는 무엇인가요?
FabricClient와 FabricGateway는 상호 인증을 수행합니다. Azure AD 인증 중에는 Azure AD 통합은 서버에 클라이언트 ID를 제공하고 서버 인증서가 서버 ID를 확인하는 데 사용됩니다. Service Fabric 인증서에 대한 자세한 내용은 [X.509 인증서 및 Service Fabric][x509-certificates-and-service-fabric]을 참조하세요.

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png


