
---
title: "Azure Portal에서 Service Fabric 만들기 | Microsoft Docs"
description: "이 문서에서는 Azure 포털 및 Azure 주요 자격 증명 모음을 사용하여 Azure에 보안 서비스 패브릭 클러스터를 설정하는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 7dda9520ce3d93bf0e86bd2481ad06c268d087c7
ms.contentlocale: ko-kr
ms.lasthandoff: 08/08/2017

---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Azure 포털을 사용하여 Azure에서 서비스 패브릭 클러스터 만들기
> [!div class="op_single_selector"]
> * [Azure 리소스 관리자](service-fabric-cluster-creation-via-arm.md)
> * [Azure 포털](service-fabric-cluster-creation-via-portal.md)
> 
> 

Azure 포털을 사용하여 Azure에 보안 서비스 패브릭 클러스터를 설정하는 단계를 안내하는 단계별 가이드입니다. 이 가이드에서는 다음 단계를 안내합니다.

* 클러스터 보안에 대한 키를 관리할 주요 자격 증명 모음을 설정합니다.
* Azure 포털을 통해 Azure에서 보안 클러스터를 만듭니다.
* 인증서를 사용하여 관리자를 인증합니다.

> [!NOTE]
> Azure Active Directory를 사용한 사용자 인증 및 응용 프로그램 보안에 대한 인증서 설정 등의 고급 보안 옵션을 사용하려면 [Azure Resource Manager를 사용하여 클러스터를 만듭니다][create-cluster-arm].
> 
> 

보안 클러스터란 응용 프로그램, 서비스 및 포함된 데이터를 배포, 업그레이드 및 삭제하는 관리 작업에 무단 액세스하는 것을 방지하는 클러스터입니다. 비보안 클러스터란 언제라도 누구든지 연결하여 관리 작업을 수행할 수 있는 클러스터입니다. 비보안 클러스터를 만드는 것이 가능하지만 **보안 클러스터를 만드는 것이 좋습니다**. 비보안 클러스터는 **나중를 보안될 수 없습니다** -새 클러스터를 만들어야 합니다.

Linux 또는 Windows 클러스터인지 여부에 관계없이 보안 클러스터 만들기에 대한 개념은 같습니다. 자세한 내용 및 보안 Linux 클러스터 만들기를 위한 도우미 스크립트는 [Linux에서 보안 클러스터 만들기](service-fabric-cluster-creation-via-arm.md#secure-linux-clusters)를 참조하세요. 제공된 도우미 스크립트에서 얻은 매개 변수는 [Azure 포털에서 클러스터 만들기](#create-cluster-portal)섹션에 설명된 대로 포털에 직접 입력할 수 있습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인
이 가이드에서는 [Azure PowerShell][azure-powershell]을 사용합니다. 새로 PowerShell 세션을 시작하려면 Azure 계정에 로그인한 후 Azure 명령을 실행하기 전에 구독을 선택합니다.

Azure 계정에 로그인합니다.

```powershell
Login-AzureRmAccount
```

구독을 선택합니다.

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>주요 자격 증명 모음 설정
가이드의 이 부분에서는 Azure에서 서비스 패브릭 클러스터에 대해서와 서비스 패브릭 응용 프로그램에 대해서 주요 자격 증명 모음을 만드는 단계를 안내합니다. Key Vault에 대한 완전한 가이드는 [Key Vault 시작 가이드][key-vault-get-started]를 참조하세요.

서비스 패브릭은 X.509 인증서를 사용하여 클러스터에 보안을 적용합니다. Azure 주요 자격 증명 모음은 Azure에서 서비스 패브릭 클러스터에 대한 인증서를 관리하는 데 사용됩니다. 클러스터를 Azure에 배포할 때 서비스 패브릭 클러스터 생성을 담당하는 Azure 리소스 공급자는 주요 자격 증명 모음에서 인증서를 가져와 클러스터 VM에 설치합니다.

다음 다이어그램은 주요 자격 증명 모음, 서비스 패브릭 클러스터 및 클러스터를 만들 때 주요 자격 증명 모음에 저장된 인증서를 사용하는 Azure 리소스 공급자 간의 관계를 보여 줍니다.

![인증서 설치][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
첫 번째 단계는 특히 주요 자격 증명 모음에 대한 새로운 리소스 그룹을 생성하는 것입니다. 주요 자격 증명 모음을 자체 리소스 그룹에 두어 키 및 암호는 유실하지 않고 서비스 패브릭 클러스터가 있는 리소스 그룹과 같은 계산 및 저장소 리소스 그룹을 제거하도록 하는 것이 좋습니다. 사용자의 주요 자격 증명 모음을 가진 리소스 그룹은 그것을 사용 중인 클러스터와 동일한 지역에 있어야 합니다.

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>주요 자격 증명 모음 만들기
새 리소스 그룹에 주요 자격 증명 모음을 만듭니다. 서비스 패브릭 리소스 공급자가 인증서를 가져와 클러스터 노드에 설치하도록 허용하기 위해 주요 자격 증명 모음을 **배포에 대해 사용하도록 설정해야 합니다** .

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
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

기존 주요 자격 증명 모음이 있는 경우라면 Azure CLI를 사용하여 배포에 대해 사용하도록 설정할 수 있습니다.

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## <a name="add-certificates-to-key-vault"></a>주요 자격 증명 모음에 인증서 추가
인증서는 서비스 패브릭에서 클러스터 및 해당 응용 프로그램의 다양한 측면을 보호하기 위해 인증 및 암호화를 제공하는 데 사용됩니다. Service Fabric에서 인증서가 사용되는 방식에 대한 자세한 내용은 [Service Fabric 클러스터 보안 시나리오][service-fabric-cluster-security]를 참조하세요.

### <a name="cluster-and-server-certificate-required"></a>클러스터 및 서버 인증서(필수)
이 인증서는 클러스터를 보호하고 무단 액세스를 방지하기 위해 필요합니다. 다음 몇 가지 방법으로 클러스터 보안을 제공합니다.

* **클러스터 인증:** 클러스터 페더레이션에 대한 노드 간 통신을 인증합니다. 이 인증서로 자신의 신분을 증명할 수 있는 노드만 클러스터에 가입할 수 있습니다.
* **서버 인증:** 관리 클라이언트에 클러스터 관리 끝점을 인증하여 관리 클라이언트기 실제 클러스터와 통신하는지 알 수 있도록 합니다. 이 인증서는 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer에 대해 SSL도 제공합니다.

이를 위해 인증서는 다음 요구 사항을 충족해야 합니다.

* 인증서에 개인 키가 포함되어 있어야 합니다.
* 개인 정보 교환(.pfx) 파일로 내보낼 수 있는 키 교환용 인증서를 만들어야 합니다.
* 인증서의 주체 이름은 서비스 패브릭 클러스터 액세스에 사용되는 도메인과 일치해야 합니다. 클러스터의 HTTPS 관리 끝점 및 Service Fabric Explorer에 대해 SSL을 제공하려면 이러한 조건이 충족되어야 합니다. `.cloudapp.azure.com` 도메인에 사용되는 SSL 인증서는 CA(인증 기관)에서 얻을 수 없습니다. 클러스터에 대한 사용자 지정 도메인 이름을 획득합니다. CA에서 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

### <a name="client-authentication-certificates"></a>클라이언트 인증 인증서
추가 클라이언트 인증서가 클러스터 관리 작업을 위해 관리자를 인증합니다. 서비스 패브릭은 **관리자** 및 **읽기 전용 사용자**의 두 가지 액세스 수준을 제공합니다. 최소한 관리 액세스에 대해 단일 인증서를 사용해야 합니다. 추가 사용자 수준 액세스를 위해서는 별도 인증서를 제공해야 합니다. 액세스 역할에 대한 자세한 내용은 [Service Fabric 클라이언트의 역할 기반 액세스 제어][service-fabric-cluster-security-roles]를 참조하세요.

Service Fabric을 사용하기 위해 클라이언트 인증 인증서를 Key Vault에 업로드할 필요는 없습니다. 이러한 인증서는 클러스터 관리 권한이 있는 사용자에게 제공하기만 하면 됩니다. 

> [!NOTE]
> Azure Active Directory는 클러스터의 관리 작업을 위해 클라이언트를 인증하기 위한 권장 방법입니다. Azure Active Directory를 사용하려면 [Azure Resource Manager를 사용하여 클러스터를 만들어야][create-cluster-arm] 합니다.
> 
> 

### <a name="application-certificates-optional"></a>응용 프로그램 인증서(선택 사항)
응용 프로그램 보안을 위해 클러스터에 제한 없는 수의 인증서를 추가로 설치할 수 있습니다. 클러스터를 만들기 전에, 다음과 같이 노드에 인증서를 설치하도록 요구하는 응용 프로그램 보안 시나리오를 고려해 보세요.

* 응용 프로그램 구성 값의 암호화 및 암호 해독
* 복제 중에 노드 간 데이터 암호화 

Azure 포털을 통해 클러스터를 만들 때 응용 프로그램 인증서를 구성할 수 없습니다. 클러스터 설치 시에 응용 프로그램 인증서를 구성하려면 [Azure Resource Manager를 사용하여 클러스터를 만들어야][create-cluster-arm] 합니다. 만든 클러스터에 응용 프로그램 인증서를 추가할 수도 있습니다.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Azure 리소스 공급자 사용을 위한 인증서 서식 지정
개인 키 파일(.pfx)을 추가하고 주요 자격 증명 모음을 통해 직접 사용할 수 있습니다. 그렇지만 Azure 리소스 공급자에서는 .pfx를 base-64로 인코딩된 문자열 상태로 포함하고 개인 키 암호를 포함하는 특수한 JSON 형식으로 키를 저장해야 합니다. 이러한 요구를 수용하기 위해 키를 JSON 문자열에 배치한 후 주요 자격 증명 모음에 *암호* 로 저장해야 합니다.

이 프로세스를 보다 쉽게 수행할 수 있도록 하기 위해 PowerShell 모듈이 [GitHub에서 사용할 수 있게 제공됩니다][service-fabric-rp-helpers]. 모듈을 사용하려면 다음 단계를 수행합니다.

1. 리포지토리의 전체 콘텐츠를 로컬 디렉터리에 다운로드합니다. 
2. PowerShell 창으로 모듈을 가져옵니다.

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

이 PowerShell 모듈의 `Invoke-AddCertToKeyVault` 명령은 자동으로 인증서 개인 키 서식을 JSON 문자열에 지정하고 주요 자격 증명 모음에 업로드합니다. 이를 사용하여 클러스터 인증서 및 추가 응용 프로그램 인증서를 주요 자격 증명 모음에 추가합니다. 클러스터에 설치하려는 모든 추가 인증서에 대해 이 단계를 반복합니다.

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

노드 인증, 관리 끝점 보안 및 인증, X.509 인증서를 사용하는 모든 추가 응용 프로그램 보안 기능에 대한 인증서를 설치하는 서비스 패브릭 클러스터 Resource Manager 템플릿을 구성하기 위해 주요 자격 증명 모음에 대해 이러한 작업이 선행되어야 합니다. 이제 Azure에는 다음과 같은 설정이 구성됩니다.

* 주요 자격 증명 모음 리소스 그룹
  * 키 자격 증명 모음
    * 클러스터 서버 인증 인증서

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>Azure 포털에서 클러스터 만들기
### <a name="search-for-the-service-fabric-cluster-resource"></a>서비스 패브릭 클러스터 리소스 검색
![Azure 포털에서 서비스 패브릭 클러스터 템플릿을 검색합니다.][SearchforServiceFabricClusterTemplate]

1. [Azure Portal][azure-portal]에 로그인합니다.
2. **새로 만들기** 를 클릭하여 새 리소스 템플릿을 추가합니다. **마켓플레이스**의 **모두**에서 Service Fabric 클러스터 템플릿을 검색합니다.
3. 목록에서 **서비스 패브릭 클러스터** 를 선택합니다.
4. **Service Fabric 클러스터** 블레이드로 이동하여 **만들기**를 클릭합니다.
5. **Service Fabric 클러스터 만들기** 블레이드는 다음 4단계를 포함합니다.

#### <a name="1-basics"></a>1. 기본 사항
![새 리소스 그룹 만들기 스크린샷][CreateRG]

기본 사항 블레이드에서는 클러스터에 대한 기본 세부 정보를 제공해야 합니다.

1. 클러스터 이름을 입력합니다.
2. VM용 원격 데스크톱의 **사용자 이름** 및 **암호**를 입력합니다.
3. 클러스터가 배포될 **구독**을 선택합니다. 특히 구독이 여러 개인 경우 반드시 선택해야 합니다.
4. **새 리소스 그룹**을 만듭니다. 클러스터 이름과 같은 이름을 사용하면 나중에 쉽게 찾을 수 있으며 특히 배포를 변경하거나 클러스터를 삭제하려고 할 때 매우 유용합니다.
   
   > [!NOTE]
   > 기존 리소스 그룹을 사용해도 되지만 새 리소스 그룹을 만드는 것이 좋습니다. 이렇게 하면 필요하지 않은 클러스터를 쉽게 삭제할 수 있습니다.
   > 
   > 
5. 클러스터를 만들려는 **지역** 을 선택합니다. 주요 자격 증명 모음이 있는 동일한 지역을 사용해야 합니다.

#### <a name="2-cluster-configuration"></a>2. 클러스터 구성
![노드 형식 만들기][CreateNodeType]

클러스터 노드를 구성합니다. 노드 유형에서 VM 크기, VM 수 및 VM 속성을 정의합니다. 클러스터는 둘 이상의 노드 형식을 가질 수 있지만 주 노드 형식(포털에서 정의하는 첫 번째 노드)에는 최소한 5개의 VM이 있어야 하며, 서비스 패브릭 시스템 서비스가 배치된 노드 형식입니다. "NodeTypeName"의 기본 배치 속성은 자동으로 추가되므로 **배치 속성** 을 구성하지 마세요.

> [!NOTE]
> 여러 노드 형식 사용에 대한 일반적인 시나리오는 프런트 엔드 서비스 및 백 엔드 서비스를 포함하는 응용 프로그램입니다. 인터넷에 열려 있는 포트를 포함하고 비교적 작은 VM(D2 같은 VM 크기)에 프런트 엔드 서비스를 배치하고, 열려 있는 인터넷 연결 포트가 없고 비교적 큰 VM(D4, D6, D15 등과 같은 VM 크기)에 백 엔드 서비스를 배치하려고 합니다.
> 
> 

1. 노드 유형에 대한 이름(문자와 숫자만을 포함하는 1~12자)을 선택합니다.
2. 주 노드 형식에 대한 최소 VM **크기**는 클러스터에 대해 선택한 **내구성** 계층에 따라 결정됩니다. 내구성 계층에 대한 기본값은 브론즈입니다. 내구성에 대한 자세한 내용은 [Service Fabric 클러스터 안정성 및 내구성 선택 방법][service-fabric-cluster-capacity]을 참조하세요.
3. VM 크기 및 가격 책정 계층을 선택합니다. D 시리즈 VM에는 SSD 드라이브가 있으므로 특히 상태 저장 응용 프로그램에 권장됩니다. 부분 코어가 있거나 사용 가능한 디스크 용량이 7GB 미만인 VM SKU를 사용하지 마세요. 
4. 주 노드 형식에 대한 최소 VM **수**는 선택한 **안정성** 계층에 따라 결정됩니다. 안정성 계층에 대한 기본값은 실버입니다. 안정성에 대한 자세한 내용은 [Service Fabric 클러스터 안정성 및 내구성 선택 방법][service-fabric-cluster-capacity]을 참조하세요.
5. 노드 유형에 대한 VM 수를 선택합니다. 나중에 노드 형식의 VM 수를 늘리거나 줄일 수 있지만 주 노드 형식에서는 선택한 안정성 수준에 따라 최소 개수가 결정됩니다. 다른 노드 유형은 VM이 1대만 있어도 됩니다.
6. 사용자 지정 끝점을 구성합니다. 이 필드를 사용하면 Azure Load Balancer를 통해 응용 프로그램에 대한 공용 인터넷에 노출하려고 하는 쉼표로 구분된 포트 목록을 입력할 수 있습니다. 예를 들어 클러스터에 웹 응용 프로그램을 배포하려는 경우 여기에 "80"을 입력하여 포트 80의 트래픽이 클러스터로 이동되도록 허용합니다. 끝점에 대한 자세한 내용은 [응용 프로그램과 통신][service-fabric-connect-and-communicate-with-services]을 참조하세요.
7. 클러스터 **진단**을 구성합니다. 기본적으로 문제 해결을 돕기 위해 클러스터에서 진단이 활성화됩니다. 진단을 사용하지 않으려면 **상태** 토글을 **사용 안 함**로 변경합니다. 진단을 끄지 **않는** 것이 좋습니다.
8. 클러스터를 설정할 패브릭 업그레이드 모드를 선택합니다. 시스템이 사용 가능한 최신 버전을 자동으로 선택하고 클러스터를 최신 버전으로 업그레이드하도록 하려면 **자동**을 선택합니다. 지원되는 버전을 선택하려는 경우 모드를 **수동**으로 설정합니다.

> [!NOTE]
> 지원되는 버전의 Service Fabric이 실행되는 클러스터만 지원합니다. **수동** 모드를 선택하면 사용자가 직접 클러스터를 지원되는 버전으로 업그레이드해야 합니다. 패브릭 업그레이드 모드에 대한 자세한 내용은 [service-fabric-cluster-upgrade 문서][service-fabric-cluster-upgrade]를 참조하세요.
> 
> 

#### <a name="3-security"></a>3. 보안
![Azure 포털의 보안 구성 스크린샷][SecurityConfigs]

최종 단계는 앞서 생성된 주요 자격 증명 모음 및 인증서 정보를 사용하여 클러스터를 보호할 수 있도록 인증서 정보를 제공하는 것입니다.

* `Invoke-AddCertToKeyVault` PowerShell 명령을 사용하여 주요 자격 증명 모음에 **클러스터 인증서**를 업로드하여 획득한 출력으로 기본 인증서 필드를 채웁니다.

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* **고급 설정 구성** 확인란을 선택하여 **관리 클라이언트** 및 **읽기 전용 클라이언트**에 대한 클라이언트 인증서를 입력합니다. 이러한 필드에는 관리 클라이언트 인증서의 지문과 읽기 전용 사용자 클라이언트 인증서의 지문을 입력하면 됩니다(해당하는 경우). 관리자가 클러스터에 연결하려고 할 경우, 여기에 입력한 지문 값과 일치하는 지문을 포함하는 인증서가 있어야만 액세스 권한이 부여됩니다.  

#### <a name="4-summary"></a>4. 요약
![“배포 서비스 패브릭 클러스터”를 표시하는 시작 보드 스크린샷 ][Notifications]

클러스터 만들기를 완료하려면 **요약** 을 클릭하여 제공된 구성을 참조하거나 클러스터를 배포하는 데 사용할 Azure Resource Manager 템플릿을 다운로드합니다. 필수 설정을 입력하면 **확인** 단추가 녹색이 되고 이를 클릭하여 클러스터 만들기 프로세스를 시작할 수 있습니다.

알림에서 만들기 진행률을 볼 수 있습니다. (화면 오른쪽 위의 상태 표시줄 근처에 있는 "종" 모양 아이콘을 클릭합니다.) 클러스터를 만드는 동안 **시작 보드에 고정**을 클릭하면 **Service Fabric 클러스터 배포 중**이 **시작** 보드에 고정됩니다.

### <a name="view-your-cluster-status"></a>클러스터 상태 보기
![대시보드의 클러스터 세부 정보 스크린샷][ClusterDashboard]

클러스터를 만들면 포털에서 클러스터를 검사할 수 있습니다.

1. **찾아보기**로 이동하고 **Service Fabric 클러스터**를 클릭합니다.
2. 클러스터를 찾아 클릭합니다.
3. 이제 대시보드에서 클러스터의 공용 끝점 및 Service Fabric Explorer에 대한 링크를 포함한 클러스터 세부 정보를 볼 수 있습니다.

클러스터 대시보드 블레이드의 **노드 모니터** 섹션에 정상 및 비정상 상태인 VM 수가 표시됩니다. 클러스터 상태에 대한 자세한 내용은 [Service Fabric 상태 모델 소개][service-fabric-health-introduction]에서 볼 수 있습니다.

> [!NOTE]
> 가용성을 유지하고 상태를 보존하기 위해 Service Fabric 클러스터에서 특정 수의 노드가 항상 작동 상태를 유지해야 하며, 이 숫자를 "유지 관리 쿼럼"이라고 합니다. 따라서 [상태 전체 백업][service-fabric-reliable-services-backup-restore]을 처음으로 수행하는 경우 외에는 일반적으로 클러스터의 모든 컴퓨터를 종료하는 것은 안전하지 않습니다.
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>가상 컴퓨터 확장 집합 인스턴스 또는 클러스터 노드에 원격 연결
클러스터에서 지정한 각 NodeType에 따라 가상 컴퓨터 확장 집합이 설정됩니다. 자세한 내용은 [가상 컴퓨터 확장 집합 인스턴스 또는 클러스터 노드에 원격 연결][remote-connect-to-a-vm-scale-set]을 참조하세요.

## <a name="next-steps"></a>다음 단계
이제 관리 인증을 위해 인증서를 사용하는 보안 클러스터가 구축되었습니다. 다음으로, [클러스터에 연결](service-fabric-connect-to-secure-cluster.md)하고 [응용 프로그램 암호를 관리](service-fabric-application-secret-management.md)하는 방법을 알아봅니다.  또한 [Service Fabric 지원 옵션](service-fabric-support.md)을 알아봅니다.

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png

