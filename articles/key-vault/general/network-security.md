---
title: Azure Key Vault 방화벽 및 가상 네트워크 구성 - Azure Key Vault
description: Key Vault 네트워킹 설정에 대한 자세한 정보
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 770e0469f9c9cf8c20f0abf826a42c0584108cd9
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2021
ms.locfileid: "109845945"
---
# <a name="configure-azure-key-vault-firewalls-and-virtual-networks"></a>Azure Key Vault 방화벽 및 가상 네트워크 구성

이 문서에서는 다양한 Key Vault 방화벽 구성에 대해 자세히 알아봅니다. 이러한 설정을 구성하는 방법에 대한 단계별 지침을 따르려면 [여기](how-to-azure-key-vault-network-security.md)에 나오는 지침을 참조하세요. 

자세한 내용은 [Azure Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)를 참조하세요.

## <a name="firewall-settings"></a>방화벽 설정

이 섹션에서는 Azure Key Vault 방화벽을 구성할 수 있는 여러 가지 방법을 설명합니다.

### <a name="key-vault-firewall-disabled-default"></a>Key Vault 방화벽 사용 안 함(기본값)

기본적으로 새 키 자격 증명 모음을 만들 때 Azure Key Vault 방화벽이 사용하지 않도록 설정됩니다. 모든 애플리케이션 및 Azure 서비스는 키 자격 증명 모음에 액세스하고 키 자격 증명 모음으로 요청을 보낼 수 있습니다. 이 구성은 모든 사용자가 키 자격 증명 모음에 대한 작업을 수행할 수 있다는 의미는 아닙니다. 키 자격 증명 모음은 Azure Active Directory 인증 및 액세스 정책 권한을 요구하는 방식으로 키 자격 증명 모음에 저장된 비밀, 키 및 인증서로 제한합니다. 키 자격 증명 모음 인증에 대한 자세한 내용은 [여기](./authentication-fundamentals.md)서 키 자격 증명 모음 인증 기본 문서를 참조하세요. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault에 액세스](./access-behind-firewall.md)를 참조하세요.

### <a name="key-vault-firewall-enabled-trusted-services-only"></a>Key Vault 방화벽 사용(신뢰할 수 있는 서비스만 해당)

Key Vault 방화벽을 사용하도록 설정하면 '신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 우회하도록 허용합니다' 옵션이 제공됩니다. Azure 서비스 중 일부는 신뢰할 수 있는 서비스 목록에 없습니다. 예를 들어 Azure DevOps는 신뢰할 수 있는 서비스 목록에 없습니다. **신뢰할 수 있는 서비스 목록에 없는 서비스는 신뢰할 수 없거나 안전하지 않다는 의미가 아닙니다.** 신뢰할 수 있는 서비스 목록에는 서비스에서 실행되는 모든 코드를 Microsoft가 제어하는 서비스가 포함됩니다. 사용자가 Azure DevOps 같은 Azure 서비스에서 사용자 지정 코드를 작성할 수 있기 때문에 Microsoft에서는 서비스에 대한 전면적 승인을 만드는 옵션을 제공하지 않습니다. 뿐만 아니라 신뢰할 수 있는 서비스 목록에 표시된다고 해서 모든 시나리오에 허용되는 것은 아닙니다. 

사용하려는 서비스가 신뢰할 수 있는 서비스 목록에 있는지 확인하려면 [여기](./overview-vnet-service-endpoints.md#trusted-services)서 다음 문서를 참조하세요.
방법 가이드는 [포털, Azure CLI 및 PowerShell](how-to-azure-key-vault-network-security.md)에 대한 지침을 따르세요.

### <a name="key-vault-firewall-enabled-ipv4-addresses-and-ranges---static-ips"></a>Key Vault 방화벽 사용(IPv4 주소 및 범위 - 고정 IP)

Key Vault 방화벽을 통해 키 자격 증명 모음에 액세스하도록 특정 서비스에 권한을 부여하려면 해당 IP 주소를 키 자격 증명 모음 방화벽 허용 목록에 추가하면 됩니다. 이 구성은 고정 IP 주소 또는 잘 알려진 범위를 사용하는 서비스에 가장 적합합니다. 이 경우에는 1,000개의 CIDR 범위 제한이 있습니다.

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

키 자격 증명 모음에서 프라이빗 링크 연결을 구성하는 방법을 알아보려면 [여기](./private-link-service.md) 문서를 참조하세요.

> [!IMPORTANT]
> 방화벽 규칙이 적용되면 사용자의 요청이 허용되는 가상 네트워크 또는 IPv4 주소 범위에서 시작되는 경우에만 사용자는 Key Vault [데이터 평면](security-features.md#privileged-access) 작업을 수행할 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 이동할 수 있다고 해도 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 Key Vault 선택기도 마찬가지입니다. 방화벽 규칙이 사용자의 클라이언트 머신을 금지하는 경우 해당 사용자는 키 자격 증명 모음 목록을 확인할 수 있지만 키를 나열하지는 못합니다.

> [!NOTE]
> 다음과 같은 구성 제한 사항을 고려해야 합니다.
> * 최대한 127개 가상 네트워크 규칙 및 127개 IPv4 규칙이 허용됩니다. 
> * IP 네트워크 규칙은 공용 IP 주소에 대해서만 허용됩니다. 프라이빗 네트워크용으로 예약된 IP 주소 범위(RFC 1918에 정의)는 IP 규칙에서 허용되지 않습니다. 개인 네트워크에는 **10.** , **172.16-31** 및 **192.168.** 로 시작하는 주소가 포함됩니다. 
> * 현재 IPv4 주소만 지원됩니다.

## <a name="references"></a>참조
* ARM 템플릿 참조: [Azure Key Vault ARM 템플릿 참조](/azure/templates/Microsoft.KeyVault/vaults)
* Azure CLI 명령: [az keyvault network-rule](/cli/azure/keyvault/network-rule)
* Azure PowerShell cmdlet: [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault), [Add-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Add-azKeyVaultNetworkRule), [Remove-AzKeyVaultNetworkRule](/powershell/module/az.KeyVault/Remove-azKeyVaultNetworkRule), [Update-AzKeyVaultNetworkRuleSet](/powershell/module/az.KeyVault/Update-azKeyVaultNetworkRuleSet)

## <a name="next-steps"></a>다음 단계

* [Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)
* [Azure Key Vault 보안 개요](security-features.md)