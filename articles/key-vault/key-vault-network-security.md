---
ms.assetid: ''
title: Azure Key Vault 방화벽 및 가상 네트워크 구성
description: Key Vault 방화벽 및 가상 네트워크 구성을 위한 단계별 지침
services: key-vault
author: amitbapat
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/31/2018
ms.author: ambapat
ms.openlocfilehash: c58fc56742c0a11771bdd84e4195e6f476622a3b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346800"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault 방화벽 및 가상 네트워크 구성

이 가이드에서는 Key Vault에 대한 액세스를 제한하도록 Key Vault 방화벽 및 가상 네트워크를 구성하기 위한 단계별 지침을 설명합니다. [Key Vault에 대한 가상 네트워크 서비스 엔드포인트](key-vault-overview-vnet-service-endpoints.md)를 사용하여 Key Vault에 대한 액세스 권한을 지정된 가상 네트워크 및/또는 IPv4(인터넷 프로토콜 버전 4) 주소 범위 집합으로 제한할 수 있습니다.

> [!IMPORTANT]
> 일단 방화벽 및 가상 네트워크 규칙이 적용되면 모든 Key Vault [데이터 평면](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) 작업은 호출자 요청이 허용되는 가상 네트워크 또는 IPV4 주소 범위에서 시작된 경우에만 수행될 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 Key Vault를 찾아볼 수 있지만 해당 클라이언트 컴퓨터가 허용 목록에 없는 경우 키/암호/인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 ‘Key Vault 선택기’도 마찬가지입니다. 방화벽 규칙이 해당 사용자의 클라이언트 컴퓨터를 금지하는 경우 사용자는 Key Vault 목록을 볼 수 있지만 키를 나열할 수는 없습니다.

## <a name="azure-portal"></a>Azure portal

1. 보호하려는 Key Vault로 이동합니다.
2. **방화벽 및 가상 네트워크**를 클릭합니다.
3. **다음에서 액세스 허용**에서 **선택한 네트워크**를 클릭합니다.
4. 방화벽 및 가상 네트워크 규칙에 기존 가상 네트워크를 추가하려면 **+기존 가상 네트워크 추가**를 클릭합니다.
5. 팝업되는 새 블레이드에서 이 Key Vault에 대한 액세스를 허용하려는 구독, 가상 네트워크 및 서브넷을 선택합니다. 선택한 가상 네트워크 및 서브넷에서 서비스 엔드포인트를 사용하도록 설정하지 않은 경우 “다음 네트워크에서 서비스 엔드포인트가 사용되도록 설정되어 있지 않습니다.”라는 메시지가 표시됩니다. 나열된 가상 네트워크 및 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정하려는 경우 **사용**을 클릭합니다. 적용되는 데 최대 15분이 걸릴 수 있습니다.
6. 또한 **+새 가상 네트워크 추가**를 클릭하고 지시에 따라 새 가상 네트워크 및 서브넷을 추가하고 새로 만든 가상 네트워크 및 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정할 수도 있습니다.
7. **IP 네트워크** 아래에서 [CIDR(Classless inter-domain Routing) 표기법](https://tools.ietf.org/html/rfc4632)의 IPv4 주소 범위 또는 개별 IP 주소를 입력하여 IPv4 주소 범위를 추가할 수 있습니다.
8. **저장**을 클릭합니다.

## <a name="azure-cli-20"></a>Azure CLI 2.0

1. [Azure CLI 2.0을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)하고 [로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)합니다.

2. 사용 가능한 가상 네트워크 규칙을 나열합니다. 이 Key Vault에 대해 어떤 규칙도 설정하지 않은 경우 목록은 비어 있습니다.
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

6. 이 Key Vault를 신뢰할 수 있는 모든 서비스에서 액세스할 수 있도록 하려면 ‘무시’를 AzureServices로 설정합니다.
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mykeyvault" --bypass AzureServices
```

7. 이제는 가장 중요한 최종 단계로, 기본 작업을 ‘거부’로 설정하여 네트워크 규칙을 켭니다.
```azurecli
az keyvault update --resource-group "myresourcegroup" --name "mekeyvault" --default-action Deny
```

## <a name="azure-powershell"></a>Azure PowerShell

1. 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 설치하고 [로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.

2. 사용 가능한 가상 네트워크 규칙을 나열합니다. 이 Key Vault에 대해 어떤 규칙도 설정하지 않은 경우 목록은 비어 있습니다.
```PowerShell
(Get-AzureRmKeyVault -VaultName "mykeyvault").NetworkAcls
```

3. 기존 가상 네트워크 및 서브넷에서 Key Vault에 대한 서비스 엔드포인트를 사용하도록 설정합니다.
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzureRmVirtualNetwork
```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -VirtualNetworkResourceId $subnet.Id
```

5. 트래픽을 허용할 IP 주소 범위를 추가합니다.
```PowerShell
Add-AzureRmKeyVaultNetworkRule -VaultName "mykeyvault" -IpAddressRange "16.17.18.0/24"
```

6. 이 Key Vault를 신뢰할 수 있는 모든 서비스에서 액세스할 수 있도록 하려면 ‘무시’를 AzureServices로 설정합니다.
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -Bypass AzureServices
```

7. 이제는 가장 중요한 최종 단계로, 기본 작업을 ‘거부’로 설정하여 네트워크 규칙을 켭니다.
```PowerShell
Update-AzureRmKeyVaultNetworkRuleSet -VaultName "mykeyvault" -DefaultAction Deny
```

## <a name="references"></a>참조

* Azure CLI 2.0 명령 - [az keyvault network-rule](https://docs.microsoft.com/cli/azure/keyvault/network-rule?view=azure-cli-latest)
* Azure PowerShell cmdlet - [Get-AzureRmKeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurermkeyvault), [Add-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureRmKeyVaultNetworkRule), [Remove-AzureRmKeyVaultNetworkRule](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureRmKeyVaultNetworkRule), [Update-AzureRmKeyVaultNetworkRuleSet](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureRmKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>다음 단계

* [Key Vault의 가상 네트워크 서비스 엔드포인트](key-vault-overview-vnet-service-endpoints.md)
* [Key Vault 보안 설정](key-vault-secure-your-key-vault.md)