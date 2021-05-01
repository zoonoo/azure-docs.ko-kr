---
title: Azure PowerShell을 사용하여 Azure AD Domain Services 리소스 포리스트 만들기 | Microsoft Docs
description: 이 문서에서는 Azure PowerShell을 사용하여 온-프레미스 Active Directory Domain Services 환경에 Azure Active Directory Domain Services 리소스 포리스트 및 아웃바운드 포리스트를 만들고 구성하는 방법에 대해 알아봅니다.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619970"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Azure PowerShell을 사용하여 온-프레미스 도메인에 대한 Azure Active Directory Domain Services 리소스 포리스트 및 아웃바운드 포리스트 트러스트 만들기

암호 해시를 동기화할 수 없거나 스마트 카드를 사용하여 독점적으로 로그인하는 사용자가 자신의 암호를 모르는 환경에서는 Azure AD DS(Active Directory Domain Services)에서 리소스 포리스트를 사용할 수 있습니다. 리소스 포리스트는 Azure AD DS에서 하나 이상의 온-프레미스 AD DS 환경으로의 단방향 아웃바운드 트러스트를 사용합니다. 이 트러스트 관계를 통해 사용자, 애플리케이션 및 컴퓨터에서 Azure AD DS 관리형 도메인의 온-프레미스 도메인에 대해 인증할 수 있습니다. 리소스 포리스트에서 온-프레미스 암호 해시는 절대 동기화되지 않습니다.

![Azure AD DS에서 온-프레미스 AD DS로의 포리스트 트러스트에 대한 다이어그램](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure PowerShell을 사용하여 Azure AD DS 리소스 포리스트 만들기
> * Azure PowerShell을 사용하여 관리되는 도메인에서 단방향 아웃바운드 포리스트 트러스트 만들기
> * 관리되는 도메인 연결을 지원하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 트러스트 관계 테스트 및 유효성 검사

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> 관리되는 도메인 리소스 포리스트는 현재 Azure HDInsight 또는 Azure Files를 지원하지 않습니다. 기본 관리되는 도메인 사용자 포리스트는 이러한 추가 서비스를 모두 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].

* Azure PowerShell을 설치하고 구성합니다.
    * 필요한 경우 지침에 따라 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결](/powershell/azure/install-az-ps)합니다.
    * [Connect-AzAccount][Connect-AzAccount] cmdlet을 사용하여 Azure 구독에 로그인합니다.
* Azure AD PowerShell을 설치하고 구성합니다.
    * 필요한 경우 지침에 따라 [Azure AD PowerShell 모듈을 설치하고 Azure AD에 연결](/powershell/azure/active-directory/install-adv2)합니다.
    * [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 로그인합니다.
* Azure AD DS를 사용하도록 설정하려면 Azure AD 테넌트에 *글로벌 관리자* 권한이 필요합니다.
* 필요한 Azure AD DS 리소스를 만들려면 Azure 구독에 *기여자* 권한이 필요합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 문서에서는 Azure Portal을 사용하여 관리되는 도메인에서 아웃바운드 포리스트 트러스트를 만들고 구성합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="deployment-process"></a>배포 프로세스

이는 관리되는 도메인 리소스 포리스트와 온-프레미스 AD DS에 대한 트러스트 관계를 만드는 여러 부분으로 구성된 프로세스입니다. 다음의 개괄적인 단계는 신뢰할 수 있는 하이브리드 환경을 구축합니다.

1. 관리되는 도메인 서비스 주체를 만듭니다.
1. 관리되는 도메인 리소스 포리스트를 만듭니다.
1. 사이트 간 VPN 또는 Express 경로를 사용하여 하이브리드 네트워크 연결을 만듭니다.
1. 트러스트 관계의 관리되는 도메인 쪽을 만듭니다.
1. 트러스트 관계의 온-프레미스 AD DS 쪽을 만듭니다.

시작하기 전에 [네트워크 고려 사항, 포리스트 이름 및 DNS 요구 사항](tutorial-create-forest-trust.md#networking-considerations)을 이해하고 있어야 합니다. 관리되는 도메인 포리스트 이름은 배포된 후에는 변경할 수 없습니다.

## <a name="create-the-azure-ad-service-principal"></a>Azure AD 서비스 주체 만들기

Azure AD DS를 사용하려면 서비스 주체가 Azure AD의 데이터를 동기화해야 합니다. 이 주체는 관리되는 도메인 리소스 포리스트를 만들기 전 Azure AD 테넌트에서 만들어야 합니다.

Azure AD DS가 통신하고 자체 인증할 수 있도록 Azure AD 서비스 주체를 만듭니다. ID가 *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* 인 *도메인 컨트롤러 서비스* 라는 특정 애플리케이션 ID가 사용됩니다. 이 애플리케이션 ID를 변경하지 마세요.

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet을 사용하여 Azure AD 서비스 주체를 만듭니다.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>관리되는 도메인 리소스 포리스트 만들기

관리되는 도메인 리소스 포리스트를 만들려면 `New-AzureAaddsForest` 스크립트를 사용합니다. 이 스크립트는 다음 섹션에서 단방향 바인딩 포리스트 만들기를 포함하여 관리되는 도메인 리소스 포리스트 만들기 및 관리를 지원하는 광범위한 명령 집합의 일부입니다. 이러한 스크립트는 [PowerShell 갤러리](https://www.powershellgallery.com/)에서 사용할 수 있으며 Azure AD 엔지니어링 팀에서 디지털 서명됩니다.

1. 먼저, [New-AzResourceGroup][New-AzResourceGroup] cmdlet을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹이 *westus* 지역에 만들어집니다. 자신의 이름과 원하는 지역을 사용합니다.

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. [Install-Script][Install-Script] cmdlet을 사용하여 [PowerShell 갤러리][powershell-gallery]에서 `New-AaddsResourceForestTrust` 스크립트를 설치합니다.

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. `New-AzureAaddsForest` 스크립트에 필요한 다음 매개 변수를 검토합니다. 필수 구성 요소 **Azure PowerShell** 및 **Azure AD PowerShell** 모듈이 있는지도 확인합니다. 애플리케이션 및 온-프레미스 연결을 제공하기 위한 가상 네트워크 요구 사항을 계획했는지 확인합니다.

    | 이름                         | 스크립트 매개 변수          | 설명 |
    |:-----------------------------|---------------------------|:------------|
    | Subscription                 | *-azureSubscriptionId*    | Azure AD DS 청구에 사용되는 구독 ID입니다. [Get-AzureRMSubscription][Get-AzureRMSubscription] cmdlet을 사용하여 구독 목록을 가져올 수 있습니다. |
    | 리소스 그룹               | *-aaddsResourceGroupName* | 관리되는 도메인 및 관련 리소스에 대한 리소스 그룹의 이름입니다. |
    | Location                     | *-aaddsLocation*          | 관리되는 도메인을 호스트하는 Azure 지역입니다. 사용 가능한 지역은 [Azure AD DS에 대해 지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)을 참조하시기 바랍니다. |
    | Azure AD DS 관리자    | *-aaddsAdminUser*         | 첫 번째 관리되는 도메인 관리자의 사용자 계정 이름입니다. 이 계정은 Azure Active Directory의 기존 클라우드 사용자 계정이어야 합니다. 사용자와 스크립트를 실행하는 사용자가 *AAD DC Administrators* 그룹에 추가됩니다. |
    | Azure AD DS 도메인 이름      | *-aaddsDomainName*        | 포리스트 이름을 선택하는 방법에 대한 이전 지침에 따라 관리되는 도메인의 정규화된 도메인 이름입니다. |

    이러한 리소스가 아직 없는 경우 `New-AzureAaddsForest` 스크립트는 Azure 가상 네트워크 및 Azure AD DS 서브넷을 만들 수 있습니다. 이 스크립트는 다음과 같이 지정된 경우 워크로드 서브넷을 선택적으로 만들 수 있습니다.

    | 이름                              | 스크립트 매개 변수                  | 설명 |
    |:----------------------------------|:----------------------------------|:------------|
    | 가상 네트워크 이름              | *-aaddsVnetName*                  | 관리되는 도메인에 대한 가상 네트워크의 이름입니다.|
    | 주소 공간                     | *-aaddsVnetCIDRAddressSpace*      | 가상 네트워크의 주소 범위가 CIDR 표기법으로 되어 있습니다(가상 네트워크를 만드는 경우).|
    | Azure AD DS 서브넷 이름           | *-aaddsSubnetName*                | 관리되는 도메인을 호스팅하는 *aaddsVnetName* 가상 네트워크의 서브넷 이름입니다. VM 및 워크로드를 이 서브넷에 배포하지 마세요. |
    | Azure AD DS 주소 범위         | *-aaddsSubnetCIDRAddressRange*    | AAD DS 인스턴스에 대한 CIDR 표기법의 서브넷 주소 범위(예: *192.168.1.0/24*)입니다. 주소 범위는 가상 네트워크의 주소 범위에 포함되어야 하며 다른 서브넷과는 다릅니다. |
    | 워크로드 서브넷 이름(선택 사항)   | *-workloadSubnetName*             | 사용자 고유의 애플리케이션 워크로드를 위해 만들 *aaddsVnetName* 가상 네트워크에 있는 서브넷의 이름(선택 사항)입니다. VM 및 애플리케이션을 대신하여 피어링된 Azure 가상 네트워크에 연결해야 합니다. |
    | 작업 주소 범위(선택 사항) | *-workloadSubnetCIDRAddressRange* | *192.168.2.0/24* 와 같은 애플리케이션 워크로드에 대한 CIDR 표기법의 서브넷 주소 범위(선택 사항)입니다. 주소 범위는 가상 네트워크의 주소 범위에 포함되어야 하며 다른 서브넷과는 다릅니다.|

1. 이제 `New-AzureAaaddsForest` 스크립트를 사용하여 관리되는 도메인 리소스 포리스트를 만듭니다. 다음 예제에서는 *addscontoso.com* 이라는 포리스트를 만들고 워크로드 서브넷을 만듭니다. 사용자 고유의 매개 변수 이름 및 IP 주소 범위 또는 기존 가상 네트워크를 제공합니다.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    관리되는 도메인 리소스 포리스트와 지원 리소스를 만드는 데에는 상당한 시간이 소요됩니다. 스크립트가 완료될 때까지 기다립니다. Azure AD 리소스 포리스트가 백그라운드에서 프로비전되는 동안 온-프레미스 네트워크 연결을 구성하려면 다음 섹션을 계속 진행합니다.

## <a name="configure-and-validate-network-settings"></a>네트워크 설정 구성 및 유효성 검사

관리되는 도메인은 계속해서 하이브리드 네트워크 연결을 온-프레미스 데이터 센터에 배포하고, 구성하고, 유효성을 검사합니다. 정기적인 유지 관리를 위해 관리되는 도메인과 함께 사용할 관리 VM도 필요합니다. 일부 하이브리드 연결이 사용자 환경에 이미 존재하거나, 팀의 다른 사용자와 협력하여 연결을 구성해야 할 수 있습니다.

시작하기 전에 [네트워크 고려 사항 및 권장 사항](tutorial-create-forest-trust.md#networking-considerations)을 이해하고 있어야 합니다.

1. Azure VPN 또는 Azure ExpressRoute 연결을 사용하여 Azure에 온-프레미스 네트워크에 대한 하이브리드 연결을 만듭니다. 하이브리드 네트워크 구성은 이 설명서의 범위를 벗어나 사용자 환경에 이미 있을 수 있습니다. 특정 시나리오에 대한 세부 사항은 다음 문서를 참조하세요.

    * [Azure 사이트 간 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
    * [Azure ExpressRoute 개요](../expressroute/expressroute-introduction.md).

    > [!IMPORTANT]
    > 관리되는 도메인의 가상 네트워크에 직접 연결을 만드는 경우 별도의 게이트웨이 서브넷을 사용합니다. 관리되는 도메인의 서브넷에서 게이트웨이를 만들지 마세요.

1. 관리되는 도메인을 관리하려면 관리 VM을 만들고, 관리되는 도메인에 조인하고, 필요한 AD DS 관리 도구를 설치합니다.

    관리되는 도메인 리소스 포리스트를 배포하는 동안, [Windows Server VM을 만든](./join-windows-vm.md) 후 [핵심 AD DS 관리 도구를 설치](./tutorial-create-management-vm.md)하여 필요한 관리 도구를 설치합니다. 도메인을 성공적으로 배포한 후 다음 단계 중 하나가 완료될 때까지 관리 VM이 관리되는 도메인에 조인되기를 기다립니다.

1. 온-프레미스 네트워크와 Azure 가상 네트워크 간의 네트워크 연결의 유효성을 검사합니다.

    * 예를 들어, 온-프레미스 도메인 컨트롤러를 `ping` 또는 원격 데스크톱을 사용하여 관리되는 VM에 연결할 수 있는지 확인합니다.
    * 다시 `ping`과 같은 유틸리티를 사용하여 온-프레미스 도메인 컨트롤러에 관리 VM을 연결할 수 있는지 확인합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다. *aaddscontoso.com* 과 같은 관리되는 도메인을 선택하고 상태가 **실행 중** 으로 보고될 때까지 기다립니다.

    실행되는 경우 [Azure 가상 네트워크에 대한 DNS 설정을 업데이트](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)한 후 [Azure AD DS에 대한 사용자 계정을 사용하도록 설정](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)하여 관리되는 도메인 리소스 포리스트의 구성을 마무리합니다.

1. 개요 페이지에 표시된 DNS 주소를 기록해 둡니다. 다음 섹션에서 트러스트 관계의 온-프레미스 Active Directory 쪽을 구성할 때 이러한 주소가 필요합니다.
1. 관리 VM을 다시 시작하여 새 DNS 설정을 수신하고 [관리되는 도메인에 VM을 조인](join-windows-vm.md#join-the-vm-to-the-managed-domain)합니다.
1. 관리 VM이 관리되는 도메인에 조인된 후 원격 데스크톱을 사용하여 다시 연결합니다.

    명령 프롬프트에서 `nslookup` 및 관리되는 도메인 리소스 포리스트 이름을 사용하여 리소스 포리스트의 이름 확인에 대한 유효성을 검사합니다.

    ```console
    nslookup aaddscontoso.com
    ```

    명령은 리소스 포리스트에 대해 두 개의 IP 주소를 반환해야 합니다.

## <a name="create-the-forest-trust"></a>포리스트 트러스트 만들기

포리스트 트러스트는 두 부분으로 구성됩니다. 관리되는 도메인 리소스 포리스트의 단방향 아웃바운드 포리스트 트러스트와 온-프레미스 AD DS 포리스트에서 단방향 인바운드 포리스트 트러스트입니다. 이 트러스트 관계의 양쪽 모두를 수동으로 만듭니다. 양쪽을 만들 때 사용자와 리소스는 포리스트 트러스트를 사용하여 성공적으로 인증할 수 있습니다. 관리되는 도메인 리소스 포리스트는 온-프레미스 포리스트에 대해 최대 5개의 단방향 아웃바운드 포리스트 트러스트를 지원합니다.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>트러스트 관계의 관리되는 도메인 쪽 만들기

트러스트 관계의 관리되는 도메인 쪽을 만들려면 `Add-AaddsResourceForestTrust` 스크립트를 사용합니다. 먼저, 다음과 같이 [Install-Script][Install-Script] cmdlet을 사용하여 [PowerShell 갤러리][powershell-gallery]에 `Add-AaddsResourceForestTrust` 스크립트를 설치합니다.

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

이제 다음 정보를 스크립트에 제공합니다.

| 이름                               | 스크립트 매개 변수     | 설명 |
|:-----------------------------------|:---------------------|:------------|
| Azure AD DS 도메인 이름            | *-ManagedDomainFqdn* | 관리되는 도메인의 정규화된 도메인 이름(예: *aaddscontoso.com*) |
| 온-프레미스 AD DS 도메인 이름      | *-TrustFqdn*         | 트러스트된 포리스트의 정규화된 도메인 이름(예: *onprem.contoso.com*) |
| 트러스트 식별 이름                | *-TrustFriendlyName* | 트러스트 관계의 식별 이름입니다. |
| 온-프레미스 AD DS DNS IP 주소 | *-TrustDnsIPs*       | 나열된 트러스트된 도메인의 DNS 서버 IPv4 주소를 쉼표로 구분한 목록입니다. |
| 트러스트 암호                     | *-TrustPassword*     | 트러스트 관계에 대한 복잡한 암호입니다. 온-프레미스 AD DS에서 단방향 인바운드 트러스트를 만들 때에도 이 암호를 입력합니다. |
| 자격 증명                        | *-Credentials*       | Azure에 인증하는 데 사용되는 자격 증명입니다. 사용자는 *AAD DC Administrators 그룹* 에 있어야 합니다. 제공되지 않는 경우 이 스크립트는 인증 메시지를 표시합니다. |

다음 예에서는 *myAzureADDSTrust* 라는 트러스트 관계를 *onprem.contoso.com* 에 만듭니다. 사용자 고유의 매개 변수 이름 및 암호를 사용합니다.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> 트러스트 암호를 잊지 마세요. 트러스트의 온-프레미스 쪽을 만들 때 동일한 암호를 사용해야 합니다.

## <a name="configure-dns-in-the-on-premises-domain"></a>온-프레미스 도메인에서 DNS 구성

온-프레미스 환경에서 관리되는 도메인을 올바르게 확인하려면 전달자를 기존 DNS 서버에 추가해야 할 수 있습니다. 관리되는 도메인과 통신하도록 온-프레미스 환경을 구성하지 않은 경우 온-프레미스 AD DS 도메인의 관리 워크스테이션에서 다음 단계를 완료합니다.

1. **시작 | 관리 도구 | DNS** 를 차례로 선택합니다.
1. 마우스 오른쪽 단추로 DNS 서버(예: *myAD01*)를 선택하고, **속성** 을 선택합니다.
1. **전달자** 를 선택한 다음, **편집** 을 선택하여 추가 전달자를 추가합니다.
1. 관리되는 도메인의 IP 주소를 추가합니다(예: *10.0.1.4* 및 *10.0.1.5*).
1. 로컬 명령 프롬프트에서 관리되는 도메인 리소스 포리스트 도메인 이름의 **nslookup** 을 사용하여 이름 확인의 유효성을 검사합니다. 예를 들어, `Nslookup aaddscontoso.com`은 관리되는 도메인 리소스 포리스트에 대해 두 개의 IP 주소를 반환합니다.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>온-프레미스 도메인에서 인바운드 포리스트 트러스트 만들기

온-프레미스 AD DS 도메인에는 관리되는 도메인에 대해 들어오는 포리스트 트러스트가 필요합니다. 이 트러스트는 온-프레미스 AD DS 도메인에서 수동으로 만들어야 하며, Azure Portal에서는 만들 수 없습니다.

온-프레미스 AD DS 도메인에서 인바운드 트러스트를 구성하려면 온-프레미스 AD DS 도메인의 관리 워크스테이션에서 다음 단계를 완료합니다.

1. **시작 | 관리 도구 | Active Directory 도메인 및 트러스트** 를 차례로 선택합니다.
1. 마우스 오른쪽 단추로 도메인(예: *onprem.contoso.com*)을 선택하고, **속성** 을 선택합니다.
1. **트러스트** 탭, **새 트러스트** 를 차례로 선택합니다.
1. *aaddscontoso.com* 과 같이 관리되는 도메인의 이름을 입력하고 **다음** 을 선택합니다.
1. **포리스트 트러스트** 를 만드는 옵션, **단방향: 들어오는 트러스트** 를 만드는 옵션을 차례로 선택합니다.
1. **이 도메인만** 에 대한 트러스트를 만들도록 선택합니다. 다음 단계에서는 Azure Portal에서 관리되는 도메인에 대한 트러스트를 만듭니다.
1. **전체 포리스트 인증** 을 사용하도록 선택한 다음, 트러스트 암호를 입력하고 확인합니다. 다음 섹션의 Azure Portal에서도 동일한 암호가 입력됩니다.
1. 기본 옵션을 사용하여 다음 몇 개의 창을 단계별로 실행한 다음, **아니요, 보내는 트러스트를 확인하지 않습니다.** 옵션을 선택합니다. 관리되는 도메인 리소스 포리스트에 위임된 관리자 계정에 필수 권한이 없기 때문에 트러스트 관계의 유효성을 검사할 수 없습니다. 이 동작은 의도된 것입니다.
1. **마침** 을 선택합니다.

## <a name="validate-resource-authentication"></a>리소스 인증 유효성 검사

다음과 같은 일반적인 시나리오를 통해 포리스트 트러스트에서 사용자 및 리소스에 대한 액세스를 올바르게 인증하는지 확인할 수 있습니다.

* [Azure AD DS 리소스 포리스트의 온-프레미스 사용자 인증](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [온-프레미스 사용자를 사용하여 Azure AD DS 리소스 포리스트의 리소스에 액세스](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [파일 및 프린터 공유 사용](#enable-file-and-printer-sharing)
    * [보안 그룹 만들기 및 멤버 추가](#create-a-security-group-and-add-members)
    * [포리스트 간 액세스를 위한 파일 공유 만들기](#create-a-file-share-for-cross-forest-access)
    * [리소스에 대한 포리스트 간 인증 유효성 검사](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS 리소스 포리스트의 온-프레미스 사용자 인증

Windows Server 가상 머신을 관리되는 도메인 리소스 도메인에 조인해야 합니다. 이 가상 머신을 사용하여 온-프레미스 사용자가 가상 머신에서 인증할 수 있는지 테스트합니다.

1. 원격 데스크톱 및 관리되는 도메인 관리자 자격 증명을 사용하여 관리되는 도메인 리소스 포리스트에 조인된 Windows Server VM에 연결합니다. NLA(네트워크 수준 인증) 오류가 발생하는 경우 사용된 사용자 계정이 도메인 사용자 계정이 아닌지 확인합니다.

    > [!TIP]
    > Azure AD Domain Services에 조인된 VM에 안전하게 연결하려면 지원되는 Azure 지역에서 [Azure Bastion 호스트 서비스](../bastion/bastion-overview.md)를 사용할 수 있습니다.

1. 명령 프롬프트를 열고, `whoami` 명령을 사용하여 현재 인증된 사용자의 고유 이름을 표시합니다.

    ```console
    whoami /fqdn
    ```

1. `runas` 명령을 사용하여 온-프레미스 도메인에서 사용자로 인증합니다. 다음 명령에서 `userUpn@trusteddomain.com`을 트러스트된 온-프레미스 도메인의 사용자 UPN으로 바꿉니다. 이 명령은 사용자의 암호를 묻는 메시지를 표시합니다.

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 인증에 성공하면 새 명령 프롬프트가 열립니다. 새 명령 프롬프트의 제목에 `running as userUpn@trusteddomain.com`이 포함되어 있습니다.
1. 새 명령 프롬프트에서 `whoami /fqdn`을 사용하여 온-프레미스 Active Directory에서 인증된 사용자의 고유 이름을 확인합니다.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>온-프레미스 사용자를 사용하여 Azure AD DS 리소스 포리스트의 리소스에 액세스

관리되는 도메인 리소스 포리스트에 조인된 Windows Server VM을 사용하면 온-프레미스 도메인의 사용자를 통해 온-프레미스 도메인에 있는 컴퓨터에서 인증할 때 해당 사용자가 리소스 포리스트에 호스팅된 리소스에 액세스할 수 있는 시나리오를 테스트할 수 있습니다. 다음 예에서는 다양한 일반적인 시나리오를 만들고 테스트하는 방법을 보여 줍니다.

#### <a name="enable-file-and-printer-sharing"></a>파일 및 프린터 공유 사용

1. 원격 데스크톱 및 관리되는 도메인 관리자 자격 증명을 사용하여 관리되는 도메인 리소스 포리스트에 조인된 Windows Server VM에 연결합니다. NLA(네트워크 수준 인증) 오류가 발생하는 경우 사용된 사용자 계정이 도메인 사용자 계정이 아닌지 확인합니다.

    > [!TIP]
    > Azure AD Domain Services에 조인된 VM에 안전하게 연결하려면 지원되는 Azure 지역에서 [Azure Bastion 호스트 서비스](../bastion/bastion-overview.md)를 사용할 수 있습니다.

1. **Windows 설정** 을 연 다음, **네트워크 및 공유 센터** 를 검색하여 선택합니다.
1. **고급 공유 설정 변경** 옵션을 선택합니다.
1. **도메인 프로필** 아래에서 **파일 및 프린터 공유 켜기**, **변경 내용 저장** 을 차례로 선택합니다.
1. **네트워크 및 공유 센터** 를 닫습니다.

#### <a name="create-a-security-group-and-add-members"></a>보안 그룹 만들기 및 멤버 추가

1. **Active Directory 사용자 및 컴퓨터** 를 엽니다.
1. 마우스 오른쪽 단추로 도메인 이름을 선택하고, **새로 만들기** 를 선택한 다음, **조직 구성 단위** 를 선택합니다.
1. 이름 상자에서 *LocalObjects* 를 입력한 다음, **확인** 을 선택합니다.
1. 탐색 창에서 **LocalObjects** 를 선택하고 마우스 오른쪽 단추로 클릭합니다. **새로 만들기**, **그룹** 을 차례로 선택합니다.
1. **그룹 이름** 상자에서 *FileServerAccess* 를 입력합니다. **그룹 범위** 에 대해 **도메인 로컬**, **확인** 을 차례로 선택합니다.
1. 내용 창에서 **FileServerAccess** 를 두 번 클릭합니다. **멤버**, **추가**, **위치** 를 차례로 선택합니다.
1. **위치** 보기에서 온-프레미스 Active Directory를 선택한 다음, **확인** 을 선택합니다.
1. **선택할 개체 이름 입력** 상자에서 *도메인 사용자* 를 입력합니다. **이름 확인** 을 선택하고, 온-프레미스 Active Directory에 대한 자격 증명을 제공한 다음, **확인** 을 선택합니다.

    > [!NOTE]
    > 단방향 트러스트 관계이므로 자격 증명을 제공해야 합니다. 즉, 관리되는 도메인의 사용자가 리소스에 액세스할 수 없거나 트러스트된 (온-프레미스) 도메인에서 사용자 또는 그룹에 대해 검색할 수 없습니다.

1. 온-프레미스 Active Directory의 **도메인 사용자** 그룹은 **FileServerAccess** 그룹의 멤버여야 합니다. **확인** 을 선택하여 해당 그룹을 저장하고 창을 닫습니다.

#### <a name="create-a-file-share-for-cross-forest-access"></a>포리스트 간 액세스를 위한 파일 공유 만들기

1. 관리되는 도메인 리소스 포리스트에 조인된 Windows Server VM에서 폴더를 만들어 *CrossForestShare* 와 같은 이름을 제공합니다.
1. 마우스 오른쪽 단추로 폴더를 선택하고, **속성** 을 선택합니다.
1. **보안** 탭, **편집** 을 차례로 선택합니다.
1. *CrossForestShare 권한* 대화 상자에서 **추가** 를 선택합니다.
1. **선택할 개체 이름 입력** 에서 *FileServerAccess* 를 입력한 다음, **확인** 을 선택합니다.
1. **그룹 또는 사용자 이름** 목록에서 *FileServerAccess* 를 선택합니다. **FileServerAccess 권한** 목록에서 **수정** 및 **쓰기** 권한에 대해 *허용* 을 선택한 다음, **확인** 을 선택합니다.
1. **공유** 탭, **고급 공유...** 를 차례로 선택합니다.
1. **이 폴더를 공유함** 을 선택한 다음, **공유 이름** 에서 기억하기 쉬운 파일 공유 이름(예: *CrossForestShare*)을 입력합니다.
1. **권한** 을 선택합니다. **모든 사용자 권한** 목록에서 **변경** 권한에 대해 **허용** 을 선택합니다.
1. **확인** 을 두 번 선택한 다음, **닫기** 를 선택합니다.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>리소스에 대한 포리스트 간 인증 유효성 검사

1. 온-프레미스 Active Directory의 사용자 계정을 사용하여 온-프레미스 Active Directory에 조인된 Windows 컴퓨터에 로그인합니다.
1. **Windows 탐색기** 에서 정규화된 호스트 이름과 공유를 사용하여 만든 공유(예: `\\fs1.aaddscontoso.com\CrossforestShare`)에 연결합니다.
1. 쓰기 권한의 유효성을 검사하려면 마우스 오른쪽 단추로 폴더를 선택하고, **새로 만들기** 를 선택한 다음, **텍스트 문서** 를 선택합니다. **새 텍스트 문서** 라는 기본 이름을 사용합니다.

    쓰기 권한이 올바르게 설정되었으면 새 텍스트 문서가 만들어집니다. 그러면 다음 단계에서 해당 파일을 열고, 편집하고, 삭제합니다.
1. 읽기 권한의 유효성을 검사하려면 **새 텍스트 문서** 를 엽니다.
1. 수정 권한의 유효성을 검사하려면 텍스트를 파일에 추가하고 **메모장** 을 닫습니다. 변경 내용을 저장하라는 메시지가 표시되면 **저장** 을 선택합니다.
1. 삭제 권한의 유효성을 검사하려면 마우스 오른쪽 단추로 **새 텍스트 문서** 를 선택하고 **삭제** 를 선택합니다. **예** 를 선택하여 파일 삭제를 확인합니다.

## <a name="update-or-remove-outbound-forest-trust"></a>아웃바운드 포레스트 트러스트 업데이트 또는 제거

관리되는 도메인에서 기존의 단방향 아웃바운드 포리스트를 업데이트해야 하는 경우 `Get-AaddsResourceForestTrusts` 및 `Set-AaddsResourceForestTrust` 스크립트를 사용할 수 있습니다. 이러한 스크립트는 포리스트 트러스트 식별 이름 또는 트러스트 암호를 업데이트하려는 경우에 유용합니다. 관리되는 도메인에서 단방향 아웃바운드 트러스트를 제거하려면 `Remove-AaddsResourceForestTrust` 스크립트를 사용할 수 있습니다. 연결된 온-프레미스 AD DS 포리스트에서 단방향 인바운드 포리스트 트러스트를 수동으로 제거해야 합니다.

### <a name="update-a-forest-trust"></a>포리스트 트러스트 업데이트

정상적인 작업에서 관리되는 도메인 포리스트와 온-프레미스 포리스트는 서로 정기적인 암호 업데이트 프로세스를 협상합니다. 이는 정상적인 AD DS 트러스트 관계 보안 프로세스의 일부입니다. 트러스트 관계에 문제가 발생하여 수동으로 알려진 암호로 다시 설정하려는 경우가 아니면 트러스트 암호를 수동으로 회전할 필요가 없습니다. 자세한 내용은 [트러스트된 도메인 개체 암호 변경](concepts-forest-trust.md#tdo-password-changes)을 참조하세요.

다음 예제 단계는 아웃바운드 트러스트 암호를 수동으로 다시 설정해야 하는 경우 기존의 트러스트 관계를 업데이트하는 방법을 보여줍니다.

1. 다음과 같이 [Install-Script][Install-Script] cmdlet을 사용하여 [PowerShell 갤러리][powershell-gallery]에서 `Get-AaddsResourceForestTrusts` 및 `Set-AaddsResourceForestTrust` 스크립트를 설치합니다.

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. 기존 트러스트를 업데이트하기 전에 먼저 `Get-AaddsResourceForestTrusts` 스크립트를 사용하여 트러스트 리소스를 가져옵니다. 다음 예제에서 기존 트러스트가 *existingTrust* 라는 개체에 할당됩니다. 다음과 같이 업데이트할 자체 관리되는 도메인 포리스트 이름 및 온-프레미스 포리스트 이름을 지정합니다.

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. 기존 트러스트 암호를 업데이트하려면 `Set-AaddsResourceForestTrust` 스크립트를 사용합니다. 이전 단계에서 기존 트러스트 개체를 지정하고 새 트러스트 관계 암호를 지정합니다. PowerShell에서 암호 복잡성을 적용하지 않으므로 사용자 환경에 대한 보안 암호를 만들고 사용해야 합니다.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>포리스트 트러스트 삭제

관리되는 도메인에서 온-프레미스 AD DS 포리스트로의 단방향 아웃바운드 포리스트 트러스트가 더 이상 필요하지 않은 경우 제거할 수 있습니다. 트러스트를 제거하기 전에 온-프레미스 AD DS 포리스트에 대해 인증해야 하는 애플리케이션이나 서비스가 없는지 확인합니다. 온-프레미스 AD DS 포리스트에서 단방향 인바운드 트러스트를 수동으로 제거해야 합니다.

1. 다음과 같이 [Install-Script][Install-Script] cmdlet을 사용하여 [PowerShell 갤러리][powershell-gallery]에서 `Remove-AaddsResourceForestTrust` 스크립트를 설치합니다.

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. 이제 `Remove-AaddsResourceForestTrust` 스크립트를 사용하여 포리스트 트러스트를 제거합니다. 다음 예제에서는 *aaddscontoso.com* 이라는 관리되는 도메인 포리스트와 온-프레미스 포리스트 *onprem.contoso.com* 간에 *myAzureADDSTrust* 라는 트러스트가 제거됩니다. 다음과 같이 제거할 자체 관리되는 도메인 포리스트 이름 및 온-프레미스 포리스트 이름을 지정합니다.

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

온-프레미스 AD DS 포리스트에서 단방향 인바운드 트러스트를 제거하려면 온-프레미스 AD DS 포리스트에 액세스할 수 있는 관리 컴퓨터에 연결하고 다음 단계를 완료합니다.

1. **시작 | 관리 도구 | Active Directory 도메인 및 트러스트** 를 차례로 선택합니다.
1. 마우스 오른쪽 단추로 도메인(예: *onprem.contoso.com*)을 선택하고, **속성** 을 선택합니다.
1. **트러스트** 탭을 선택하고 관리되는 도메인 포리스트에서 기존의 받는 트러스트를 선택합니다.
1. **제거** 를 선택한 후 받는 트러스트를 제거할지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure PowerShell을 사용하여 관리되는 도메인 리소스 포리스트 만들기
> * Azure PowerShell을 사용하여 관리되는 도메인에서 단방향 아웃바운드 포리스트 트러스트 만들기
> * 관리되는 도메인 연결을 지원하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 트러스트 관계 테스트 및 유효성 검사

Azure AD DS의 포리스트 유형에 대한 자세한 개념 정보는 [리소스 포리스트란?][concepts-forest] 및 [Azure AD DS에서 포리스트 트러스트가 작동하는 방법][concepts-trust]을 참조하세요.

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/