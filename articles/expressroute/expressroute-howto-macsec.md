---
title: 'Azure 익스프레스라우팅: MACsec 구성'
description: 이 문서에서는 에지 라우터와 Microsoft의 에지 라우터 간의 연결을 보호하기 위해 MACsec을 구성하는 데 도움이 됩니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083542"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>익스프레스루트 다이렉트 포트에서 MACsec 구성

이 문서에서는 PowerShell을 사용하여 에지 라우터와 Microsoft의 에지 라우터 간의 연결을 보호하도록 MACsec을 구성하는 데 도움이 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음을 확인합니다.

* [ExpressRoute 직접 프로비저닝 워크플로를 이해합니다.](expressroute-erdirect-about.md)
* [ExpressRoute 직접 포트 리소스를](expressroute-howto-erdirect.md)만들었습니다.
* PowerShell을 로컬로 실행하려면 최신 버전의 Azure PowerShell이 컴퓨터에 설치되어 있는지 확인합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>로그인하고 올바른 구독을 선택합니다.

구성을 시작하려면 Azure 계정에 로그인하고 사용할 구독을 선택합니다.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Azure 키 볼트, MACsec 비밀 및 사용자 ID 만들기

1. 키 볼트 인스턴스를 만들어 MACsec 비밀을 새 리소스 그룹에 저장합니다.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    키 자격 증명 모음 또는 리소스 그룹이 이미 있는 경우 다시 사용할 수 있습니다. 그러나 기존 키 자격 증명 모음에서 [ **소프트 삭제** 기능을](../key-vault/key-vault-ovw-soft-delete.md) 사용하도록 설정하는 것이 중요합니다. 소프트 삭제를 사용하도록 설정하지 않은 경우 다음 명령을 사용하여 활성화할 수 있습니다.

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 사용자 ID를 만듭니다.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    New-AzUserAssignedId가 유효한 PowerShell cmdlet으로 인식되지 않으면 다음 모듈(관리자 모드)을 설치하고 위의 명령을 다시 실행합니다.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Create a connectivity association key (CAK) and a connectivity association key name (CKN) and store them in the key vault.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. 사용자 ID에 GET 권한을 할당합니다.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   이제 이 ID는 키 자격 증명 모음에서 CAK 및 CKN과 같은 비밀을 얻을 수 있습니다.
5. ExpressRoute에서 사용할 사용자 ID를 설정합니다.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. 익스프레스루트 다이렉트 포트에서 MACsec 구성

### <a name="to-enable-macsec"></a>MACsec을 사용하려면

각 ExpressRoute Direct 인스턴스에는 두 개의 물리적 포트가 있습니다. 두 포트에서 동시에 MACsec을 사용하도록 선택하거나 한 번에 하나의 포트에서 MACsec을 사용하도록 설정할 수 있습니다. 다른 포트를 서비스하는 동안 트래픽을 활성 포트로 전환하여 한 번에 하나의 포트를 수행하면 ExpressRoute Direct가 이미 서비스 중인 경우 중단을 최소화할 수 있습니다.

1. 필요한 경우 ExpressRoute 관리 코드가 MACsec 비밀에 액세스할 수 있도록 MACsec 암호와 암호를 설정하고 사용자 ID를 포트와 연결합니다.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (선택 사항) 포트가 관리 다운 상태에 있는 경우 다음 명령을 실행하여 포트를 가져올 수 있습니다.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    이 시점에서 MACsec은 마이크로소프트 측의 익스프레스루트 다이렉트 포트에서 활성화됩니다. 에지 장치에서 구성하지 않은 경우 동일한 MACsec 암호 및 암호로 구성할 수 있습니다.

### <a name="to-disable-macsec"></a>MACsec을 사용하지 않도록 설정하려면

ExpressRoute Direct 인스턴스에서 MACsec이 더 이상 필요하지 않은 경우 다음 명령을 실행하여 비활성화할 수 있습니다.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

이 시점에서 MACsec은 Microsoft 측의 익스프레스루트 직접 포트에서 비활성화됩니다.

### <a name="test-connectivity"></a>연결 테스트
ExpressRoute Direct 포트에서 MACsec(MACsec 키 업데이트 포함)을 구성한 후 회로의 BGP 세션이 실행되고 있는지 [확인합니다.](expressroute-troubleshooting-expressroute-overview.md) 포트에 아직 회로가 없는 경우 먼저 하나를 만들고 회로의 Azure 개인 피어링 또는 Microsoft 피어링을 설정하십시오. MACsec이 네트워크 장치와 Microsoft의 네트워크 장치 간에 MACsec 키 불일치를 포함하여 잘못 구성된 경우 계층 2및 계층 3의 BGP 설치에서 ARP 해상도가 표시되지 않습니다. 모든 것이 올바르게 구성된 경우 양방향으로 올바르게 광고된 BGP 경로와 ExpressRoute를 통해 응용 프로그램 데이터 흐름이 표시됩니다.

## <a name="next-steps"></a>다음 단계
1. [익스프레스루트 다이렉트에서 익스프레스루트 회로 생성](expressroute-howto-erdirect.md)
2. [Azure Virtual Network에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)
3. [익스프레스루트 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
