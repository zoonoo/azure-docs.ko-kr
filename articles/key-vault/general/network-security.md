---
title: Azure Key Vault 방화벽 및 가상 네트워크 구성 - Azure Key Vault
description: Key Vault 방화벽 및 가상 네트워크 구성을 위한 단계별 지침
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: sudbalas
ms.custom: devx-track-azurecli
ms.openlocfilehash: c375defe5fd8356d64879a65d6f09f40ea30271d
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042476"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault 방화벽 및 가상 네트워크 구성

이 문서에서는 Azure Key Vault 방화벽을 구성하는 방법에 대한 지침을 제공합니다. 이 문서에서는 Key Vault 방화벽에 대한 여러 가지 구성을 자세히 설명하고, 다른 애플리케이션 및 Azure 서비스와 함께 작동하도록 Azure Key Vault를 구성하는 방법에 대한 단계별 지침을 제공합니다.

## <a name="firewall-settings"></a>방화벽 설정

이 섹션에서는 Azure Key Vault 방화벽을 구성할 수 있는 여러 가지 방법을 설명합니다.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault 방화벽 사용 안 함(기본값)

기본적으로 새 키 자격 증명 모음을 만들 때 Azure Key Vault 방화벽이 사용하지 않도록 설정됩니다. 모든 애플리케이션 및 Azure 서비스는 키 자격 증명 모음에 액세스하고 키 자격 증명 모음으로 요청을 보낼 수 있습니다. 이 구성은 모든 사용자가 키 자격 증명 모음에 대한 작업을 수행할 수 있다는 의미는 아닙니다. 키 자격 증명 모음은 Azure Active Directory 인증 및 액세스 정책 권한을 요구하는 방식으로 키 자격 증명 모음에 저장된 비밀, 키 및 인증서로 제한합니다. 키 자격 증명 모음 인증에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/key-vault/general/authentication-fundamentals)서 키 자격 증명 모음 인증 기본 문서를 참조하세요.

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault 방화벽 사용(신뢰할 수 있는 서비스만 해당)

Key Vault 방화벽을 사용하도록 설정하면 '신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 우회하도록 허용합니다' 옵션이 제공됩니다. Azure 서비스 중 일부는 신뢰할 수 있는 서비스 목록에 없습니다. 예를 들어 Azure DevOps는 신뢰할 수 있는 서비스 목록에 없습니다. **신뢰할 수 있는 서비스 목록에 없는 서비스는 신뢰할 수 없거나 안전하지 않다는 의미가 아닙니다.** 신뢰할 수 있는 서비스 목록에는 서비스에서 실행되는 모든 코드를 Microsoft가 제어하는 서비스가 포함됩니다. 사용자가 Azure DevOps 같은 Azure 서비스에서 사용자 지정 코드를 작성할 수 있기 때문에 Microsoft에서는 서비스에 대한 전면적 승인을 만드는 옵션을 제공하지 않습니다. 뿐만 아니라 신뢰할 수 있는 서비스 목록에 표시된다고 해서 모든 시나리오에 허용되는 것은 아닙니다.

사용하려는 서비스가 신뢰할 수 있는 서비스 목록에 있는지 확인하려면 [여기](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)서 다음 문서를 참조하세요.

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault 방화벽 사용(IPv4 주소 및 범위 - 고정 IP)

Key Vault 방화벽을 통해 키 자격 증명 모음에 액세스하도록 특정 서비스에 권한을 부여하려면 해당 IP 주소를 키 자격 증명 모음 방화벽 허용 목록에 추가하면 됩니다. 이 구성은 고정 IP 주소 또는 잘 알려진 범위를 사용하는 서비스에 가장 적합합니다.

웹앱 또는 논리 앱과 같은 Azure 리소스의 IP 주소 또는 범위를 허용하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인
1. 리소스(서비스의 특정 인스턴스)를 선택합니다.
1. '설정'에서 '속성' 블레이드를 클릭합니다.
1. "IP 주소" 필드를 찾습니다.
1. 이 값 또는 범위를 복사하여 키 자격 증명 모음 방화벽 허용 목록에 입력합니다.

Key Vault 방화벽을 통해 전체 Azure 서비스를 허용하려면 [여기](https://www.microsoft.com/download/details.aspx?id=41653)서 Azure에 대해 공개적으로 문서화된 데이터 센터 IP 주소 목록을 사용합니다. 원하는 지역에서 원하는 서비스와 연결된 IP 주소를 찾은 다음, 위의 단계에 따라 해당 IP 주소를 키 자격 증명 모음 방화벽에 추가합니다.

### <a name="key-vault-firewall-enabled-virtual-networks---dynamic-ips"></a>Key Vault 방화벽 사용(가상 네트워크 - 동적 IP)

키 자격 증명 모음을 통해 가상 머신과 같은 Azure 리소스를 허용하려 할 때 고정 IP 주소를 사용하지 못할 수 있으며, Azure Virtual Machines의 일부 IP 주소만 키 자격 증명 모음에 액세스하도록 허용하려는 경우가 있습니다.

이 경우 가상 네트워크 내에서 리소스를 만든 다음, 특정 가상 네트워크 및 서브넷의 트래픽이 키 자격 증명 모음에 액세스하도록 허용해야 합니다. 이렇게 하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인
1. 구성하려는 키 자격 증명 모음을 선택합니다.
1. '네트워킹' 블레이드를 선택합니다.
1. '+ 기존 가상 네트워크 추가'를 선택합니다.
1. 키 자격 증명 모음 방화벽을 통해 허용할 가상 네트워크 및 서브넷을 선택합니다.

### <a name="key-vault-firewall-enabled-private-link"></a>Key Vault 방화벽 사용(프라이빗 링크)

키 자격 증명 모음에서 프라이빗 링크 연결을 구성하는 방법을 알아보려면 [여기](https://docs.microsoft.com/azure/key-vault/general/private-link-service) 문서를 참조하세요.

> [!IMPORTANT]
> 방화벽 규칙이 적용되면 사용자의 요청이 허용되는 가상 네트워크 또는 IPv4 주소 범위에서 시작되는 경우에만 사용자는 Key Vault [데이터 평면](secure-your-key-vault.md#data-plane-access-control) 작업을 수행할 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 이동할 수 있다고 해도 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 Key Vault 선택기도 마찬가지입니다. 방화벽 규칙이 사용자의 클라이언트 머신을 금지하는 경우 해당 사용자는 키 자격 증명 모음 목록을 확인할 수 있지만 키를 나열하지는 못합니다.

> [!NOTE]
> 다음과 같은 구성 제한 사항을 고려해야 합니다.
> * 최대한 127개 가상 네트워크 규칙 및 127개 IPv4 규칙이 허용됩니다. 
> * IP 네트워크 규칙은 공용 IP 주소에 대해서만 허용됩니다. 프라이빗 네트워크용으로 예약된 IP 주소 범위(RFC 1918에 정의)는 IP 규칙에서 허용되지 않습니다. 개인 네트워크에는 **10.** , **172.16-31** 및 **192.168.** 로 시작하는 주소가 포함됩니다. 
> * 현재 IPv4 주소만 지원됩니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal을 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 방법은 다음과 같습니다.

1. 보호하려는 키 자격 증명 모음으로 이동합니다.
2. **네트워킹**을 선택한 다음, **방화벽 및 가상 네트워크** 탭을 선택합니다.
3. **다음에서 액세스 허용**에서 **선택한 네트워크**를 선택합니다.
4. 방화벽 및 가상 네트워크 규칙에 기존 가상 네트워크를 추가하려면 **+기존 가상 네트워크 추가**를 선택합니다.
5. 열리는 새 블레이드에서 이 키 자격 증명 모음에 대한 액세스를 허용하려는 구독, 가상 네트워크 및 서브넷을 선택합니다. 선택하는 가상 네트워크 및 서브넷이 서비스 엔드포인트를 사용하지 못하는 경우 서비스 엔드포인트를 사용하도록 설정했는지 확인하고 **사용하도록 설정**을 선택합니다. 적용되는 데 최대 15분이 걸릴 수 있습니다.
6. **IP 네트워크**에서 [CIDR(Classless inter-domain Routing) 표기법](https://tools.ietf.org/html/rfc4632)의 IPv4 주소 범위 또는 개별 IP 주소를 입력하여 IPv4 주소 범위를 추가합니다.
7. Microsoft Trusted Services에서 Key Vault 방화벽을 무시하도록 하려면 '예'를 선택합니다. 현재 Key Vault Trusted Services의 전체 목록은 다음 링크를 참조하세요. [Azure Key Vault Trusted Services](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
7. **저장**을 선택합니다.

또한 **+새 가상 네트워크 추가**를 선택하여 새 가상 네트워크 및 서브넷을 추가한 다음, 새로 만든 가상 네트워크 및 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정할 수 있습니다. 그런 다음, 표시되는 메시지를 따릅니다.

## <a name="use-the-azure-cli"></a>Azure CLI 사용 

Azure CLI를 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 방법은 다음과 같습니다.

1. [Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)하고 [로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)합니다.

2. 사용 가능한 가상 네트워크 규칙을 나열합니다. 이 키 자격 증명 모음에 대한 모든 규칙을 설정하지 않은 경우 목록은 비게 됩니다.
   ```azurecli
   az keyvault network-rule list --resource-group myresourcegroup --name mykeyvault
   ```

3. 기존 가상 네트워크 및 서브넷에서 Key Vault에 대한 서비스 엔드포인트를 사용하도록 설정합니다.
   ```azurecli
   az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.KeyVault"
   ```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.
   ```azurecli
   subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
   az keyvault network-rule add --resource-group "demo9311" --name "demo9311premium" --subnet $subnetid
   ```

5. 트래픽을 허용할 IP 주소 범위를 추가합니다.
   ```azurecli
   az keyvault network-rule add --resource-group "myresourcegroup" --name "mykeyvault" --ip-address "191.10.18.0/24"
   ```

6. 이 키 자격 증명 모음을 신뢰할 수 있는 모든 서비스에서 액세스할 수 있도록 하려면 `bypass`를 `AzureServices`로 설정합니다.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
   ```

7. 기본 작업을 `Deny`로 설정하여 네트워크 규칙을 켭니다.
   ```azurecli
   az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
   ```

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell을 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 방법은 다음과 같습니다.

1. 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 설치하고 [로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.

2. 사용 가능한 가상 네트워크 규칙을 나열합니다. 이 키 자격 증명 모음에 대한 모든 규칙을 설정하지 않은 경우 목록은 비게 됩니다.
   ```powershell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 기존 가상 네트워크 및 서브넷에서 Key Vault에 대한 서비스 엔드포인트를 사용하도록 설정합니다.
   ```powershell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.
   ```powershell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. 트래픽을 허용할 IP 주소 범위를 추가합니다.
   ```powershell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. 이 키 자격 증명 모음을 신뢰할 수 있는 모든 서비스에서 액세스할 수 있도록 하려면 `bypass`를 `AzureServices`로 설정합니다.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 기본 작업을 `Deny`로 설정하여 네트워크 규칙을 켭니다.
   ```powershell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>참조
* ARM 템플릿 참조: [Azure Key Vault ARM 템플릿 참조](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 명령: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell cmdlet: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>다음 단계

* [Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)
* [Key Vault 보안 설정](secure-your-key-vault.md)
