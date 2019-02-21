---
ms.assetid: ''
title: Azure Key Vault 방화벽 및 가상 네트워크 구성 - Azure Key Vault
description: Key Vault 방화벽 및 가상 네트워크 구성을 위한 단계별 지침
services: key-vault
author: amitbapat
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: 4b3225dd25fee2859a36f98add51fcf612a45c83
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108894"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault 방화벽 및 가상 네트워크 구성

이 문서에서는 Key Vault에 대한 액세스를 제한하도록 Azure Key Vault 방화벽 및 가상 네트워크를 구성하기 위한 단계별 지침을 제공합니다. [Key Vault에 대한 가상 네트워크 서비스 엔드포인트](key-vault-overview-vnet-service-endpoints.md)를 사용하면 지정된 가상 네트워크 및/또는 IPv4(인터넷 프로토콜 버전 4) 주소 범위 세트에 대한 액세스를 제한할 수 있습니다.

> [!IMPORTANT]
> 방화벽 규칙이 적용되면 사용자의 요청이 허용되는 가상 네트워크 또는 IPv4 주소 범위에서 시작되는 경우에만 사용자는 Key Vault [데이터 평면](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) 작업을 수행할 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 이동할 수 있다고 해도 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 Key Vault 선택기도 마찬가지입니다. 방화벽 규칙이 사용자의 클라이언트 머신을 금지하는 경우 해당 사용자는 키 자격 증명 모음 목록을 확인할 수 있지만 키를 나열하지는 못합니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Portal을 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 방법은 다음과 같습니다.

1. 보호하려는 키 자격 증명 모음으로 이동합니다.
2. **방화벽 및 가상 네트워크**를 선택합니다.
3. **다음에서 액세스 허용**에서 **선택한 네트워크**를 선택합니다.
4. 방화벽 및 가상 네트워크 규칙에 기존 가상 네트워크를 추가하려면 **+기존 가상 네트워크 추가**를 선택합니다.
5. 열리는 새 블레이드에서 이 키 자격 증명 모음에 대한 액세스를 허용하려는 구독, 가상 네트워크 및 서브넷을 선택합니다. 선택하는 가상 네트워크 및 서브넷이 서비스 엔드포인트를 사용하지 못하는 경우 서비스 엔드포인트를 사용하도록 설정했는지 확인하고 **사용하도록 설정**을 선택합니다. 적용되는 데 최대 15분이 걸릴 수 있습니다.
6. **IP 네트워크**에서 [CIDR(Classless inter-domain Routing) 표기법](https://tools.ietf.org/html/rfc4632)의 IPv4 주소 범위 또는 개별 IP 주소를 입력하여 IPv4 주소 범위를 추가합니다.
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 방법은 다음과 같습니다.

1. 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)을 설치하고 [로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.

2. 사용 가능한 가상 네트워크 규칙을 나열합니다. 이 키 자격 증명 모음에 대한 모든 규칙을 설정하지 않은 경우 목록은 비게 됩니다.
   ```PowerShell
   (Get-AzKeyVault -VaultName "mykeyvault").NetworkAcls
   ```

3. 기존 가상 네트워크 및 서브넷에서 Key Vault에 대한 서비스 엔드포인트를 사용하도록 설정합니다.
   ```PowerShell
   Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
   ```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.
   ```PowerShell
   $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
   ```

5. 트래픽을 허용할 IP 주소 범위를 추가합니다.
   ```PowerShell
   Add-AzKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
   ```

6. 이 키 자격 증명 모음을 신뢰할 수 있는 모든 서비스에서 액세스할 수 있도록 하려면 `bypass`를 `AzureServices`로 설정합니다.
   ```PowerShell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
   ```

7. 기본 작업을 `Deny`로 설정하여 네트워크 규칙을 켭니다.
   ```PowerShell
   Update-AzKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
   ```

## <a name="references"></a>참조

* Azure CLI 명령: [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell cmdlet: [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>다음 단계

* [Key Vault의 가상 네트워크 서비스 엔드포인트](key-vault-overview-vnet-service-endpoints.md)
* [Key Vault 보안 설정](key-vault-secure-your-key-vault.md)
