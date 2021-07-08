---
title: Azure Key Vault 네트워킹 구성을 구성하는 방법
description: Key Vault 방화벽 및 가상 네트워크 구성을 위한 단계별 지침
services: key-vault
author: sebansal
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 5/11/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: d6035436a005012cf67a46302213f79b2dce1f59
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109850193"
---
# <a name="configure-azure-key-vault-networking-settings"></a>Azure Key Vault 네트워킹 설정 구성

이 문서에서는 다른 애플리케이션 및 Azure 서비스와 작동하도록 Azure Key Vault 네트워킹 설정을 구성하는 방법에 대한 지침을 제공합니다. 다양한 네트워크 보안 구성에 대해 자세히 알아보려면 [여기를 읽어보세요](network-security.md).

Azure Portal, Azure CLI 및 Azure PowerShell을 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 단계별 지침은 다음과 같습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)


1. 보호하려는 키 자격 증명 모음으로 이동합니다.
2. **네트워킹** 을 선택한 다음, **방화벽 및 가상 네트워크** 탭을 선택합니다.
3. **다음에서 액세스 허용** 에서 **선택한 네트워크** 를 선택합니다.
4. 방화벽 및 가상 네트워크 규칙에 기존 가상 네트워크를 추가하려면 **+기존 가상 네트워크 추가** 를 선택합니다.
5. 열리는 새 블레이드에서 이 키 자격 증명 모음에 대한 액세스를 허용하려는 구독, 가상 네트워크 및 서브넷을 선택합니다. 선택하는 가상 네트워크 및 서브넷이 서비스 엔드포인트를 사용하지 못하는 경우 서비스 엔드포인트를 사용하도록 설정했는지 확인하고 **사용하도록 설정** 을 선택합니다. 적용되는 데 최대 15분이 걸릴 수 있습니다.
6. **IP 네트워크** 에서 [CIDR(Classless inter-domain Routing) 표기법](https://tools.ietf.org/html/rfc4632)의 IPv4 주소 범위 또는 개별 IP 주소를 입력하여 IPv4 주소 범위를 추가합니다.
7. Microsoft Trusted Services에서 Key Vault 방화벽을 무시하도록 하려면 '예'를 선택합니다. 현재 Key Vault Trusted Services의 전체 목록은 다음 링크를 참조하세요. [Azure Key Vault Trusted Services](./overview-vnet-service-endpoints.md#trusted-services)
7. **저장** 을 선택합니다.

또한 **+새 가상 네트워크 추가** 를 선택하여 새 가상 네트워크 및 서브넷을 추가한 다음, 새로 만든 가상 네트워크 및 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정할 수 있습니다. 그런 다음, 표시되는 메시지를 따릅니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 방법은 다음과 같습니다.

1. [Azure CLI를 설치](/cli/azure/install-azure-cli)하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

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

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell을 사용하여 Key Vault 방화벽 및 가상 네트워크를 구성하는 방법은 다음과 같습니다.

1. 최신 [Azure PowerShell](/powershell/azure/install-az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

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
---
## <a name="references"></a>참조
* ARM 템플릿 참조: [Azure Key Vault ARM 템플릿 참조](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 명령: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlet: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>다음 단계

* [Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)
* [Azure Key Vault 보안 개요](security-features.md)
