---
title: Azure Storage 방화벽 및 가상 네트워크 구성 | Microsoft Docs
description: Azure Storage 방화벽과 Azure Virtual Network를 사용 하 여 저장소 계정에 대 한 계층화 된 네트워크 보안을 구성 합니다.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c8807f0200f96dc12a3b3d43fa50a91bec85ed38
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071185"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage 방화벽 및 가상 네트워크 구성

Azure Storage는 계층화된 보안 모델을 제공합니다. 이 모델을 사용 하면 사용 하는 네트워크 또는 리소스의 유형 및 하위 집합에 따라 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 저장소 계정에 대 한 액세스 수준을 보호 하 고 제어할 수 있습니다. 네트워크 규칙이 구성 된 경우 지정 된 네트워크 집합 또는 지정 된 Azure 리소스 집합을 통해 데이터를 요청 하는 응용 프로그램만 저장소 계정에 액세스할 수 있습니다. 저장소 계정에 대 한 액세스를 지정 된 IP 주소, IP 범위, Azure Virtual Network (VNet)의 서브넷 또는 일부 Azure 서비스의 리소스 인스턴스에 대 한 요청으로 제한할 수 있습니다.

스토리지 계정에는 인터넷을 통해 액세스할 수 있는 퍼블릭 엔드포인트가 있습니다. VNet에서 스토리지 계정으로 프라이빗 IP 주소를 할당하고 프라이빗 링크를 통과하는 VNet 및 스토리지 계정 간의 모든 트래픽을 보호하는 [스토리지 계정에 대한 프라이빗 엔드포인트](storage-private-endpoints.md)를 만들 수도 있습니다. Azure storage 방화벽은 저장소 계정의 공용 끝점에 대 한 액세스 제어를 제공 합니다. 또한 프라이빗 엔드포인트를 사용할 때 방화벽을 사용하여 퍼블릭 엔드포인트를 통한 모든 액세스를 차단할 수도 있습니다. 스토리지 방화벽 구성을 사용하여 선택한 신뢰할 수 있는 Azure 플랫폼 서비스에서 스토리지 계정에 안전하게 액세스할 수 있도록 합니다.

네트워크 규칙이 적용될 때 스토리지 계정에 액세스하는 애플리케이션에는 요청에 대한 적절한 권한 부여가 필요합니다. 권한 부여는 Azure AD(Azure Active Directory) 자격 증명(Blob 및 큐용), 유효한 계정 액세스 키 또는 SAS 토큰을 통해 지원됩니다.

> [!IMPORTANT]
> 스토리지 계정에 대해 방화벽 규칙을 켜면 기본적으로 Azure VNet(Virtual Network) 내에서 작동하는 서비스 또는 허용되는 퍼블릭 IP 주소에서 요청하지 않는 한, 들어오는 데이터 요청이 차단됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다.
>
> 서비스 인스턴스를 호스트하는 서브넷의 트래픽을 허용하여 VNet 내에서 작동하는 Azure 서비스에 대한 액세스 권한을 부여할 수 있습니다. 또한 아래에 설명 된 예외 메커니즘을 통해 제한 된 수의 시나리오를 사용할 수 있습니다. Azure Portal을 통해 스토리지 계정의 데이터에 액세스하려면 설정한 신뢰할 수 있는 경계(IP 또는 VNet) 내의 머신에 있어야 합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>시나리오

스토리지 계정을 보호하려면 먼저 퍼블릭 엔드포인트에서 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 규칙을 구성해야 합니다. 그런 다음, 특정 VNet 트래픽에 대한 액세스를 허가하는 규칙을 구성해야 합니다. 또한 특정 인터넷 또는 온-프레미스 클라이언트에서 연결을 사용 하도록 설정 하 여 선택한 공용 인터넷 IP 주소 범위에서 트래픽에 대 한 액세스를 허용 하도록 규칙을 구성할 수 있습니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다.

특정 가상 네트워크의 액세스를 허용하는 방화벽 규칙과 동일한 스토리지 계정의 퍼블릭 IP 주소 범위에서의 액세스를 허용하는 방화벽 규칙을 결합할 수 있습니다. 스토리지 방화벽 규칙을 기존 스토리지 계정에 적용하거나 새 스토리지 계정을 만들 때 적용할 수 있습니다.

스토리지 방화벽 규칙은 스토리지 계정의 퍼블릭 엔드포인트에 적용됩니다. 스토리지 계정의 프라이빗 엔드포인트에 대한 트래픽을 허용하는 방화벽 액세스 규칙은 필요하지 않습니다. 프라이빗 엔드포인트의 생성을 승인하면 프라이빗 엔드포인트를 호스트하는 서브넷의 트래픽에 대해 암시적 액세스 권한이 부여됩니다.

네트워크 규칙은 REST 및 SMB를 포함 하 여 Azure storage에 대 한 모든 네트워크 프로토콜에 적용 됩니다. Azure Portal, 스토리지 탐색기 및 AZCopy와 같은 도구를 사용하여 데이터에 액세스하려면 명시적 네트워크 규칙을 구성해야 합니다.

네트워크 규칙이 적용되면 모든 요청에 적용됩니다. 특정 IP 주소에 대한 액세스 권한을 부여하는 SAS 토큰은 토큰 소유자의 액세스를 제한하지만, 구성된 네트워크 규칙 이외의 새 액세스 권한은 부여하지 않습니다.

가상 머신 디스크 트래픽(탑재 및 분리 작업 및 디스크 IO 포함)은 네트워크 규칙의 영향을 받지 않습니다. 페이지 Blob에 대한 REST 액세스는 네트워크 규칙에 의해 보호됩니다.

클래식 스토리지 계정은 방화벽 및 가상 네트워크를 지원하지 않습니다.

예외를 만들어 Vm을 백업 및 복원 하는 데 적용 되는 네트워크 규칙을 사용 하 여 저장소 계정에 관리 되지 않는 디스크를 사용할 수 있습니다. 이 프로세스는이 문서의 [예외 관리](#manage-exceptions) 섹션에 설명 되어 있습니다. 방화벽 예외는 Azure에서 이미 관리되고 있으므로 관리 디스크에는 적용되지 않습니다.

## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경

기본적으로 스토리지 계정은 네트워크에 있는 모든 클라이언트로부터의 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경하면 Azure Storage에 연결하는 애플리케이션의 기능에 영향을 미칠 수 있습니다. 기본 네트워크 규칙을 **거부** 로 설정하면 액세스를 **허용** 하는 특정 네트워크 규칙이 적용되지 않는 한 데이터에 대한 모든 액세스가 차단됩니다. 액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다.

### <a name="managing-default-network-access-rules"></a>기본 네트워크 액세스 규칙 관리

스토리지 계정에 대한 기본 네트워크 액세스 규칙은 Azure Portal, PowerShell 또는 CLIv2를 통해 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라고 하는 설정 메뉴에서를 선택 합니다.

3. 기본적으로 액세스를 거부하려면 **선택한 네트워크** 에서 액세스를 허용하도록 선택합니다. 모든 네트워크의 트래픽을 허용하려면 **모든 네트워크** 에서 액세스를 허용하도록 선택합니다.

4. **저장** 을 선택하여 변경 내용을 적용합니다.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

2. 스토리지 계정에 대한 기본 규칙의 상태를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가

특정 서브넷에서만 액세스를 허용하도록 스토리지 계정을 구성할 수 있습니다. 허용되는 서브넷은 동일한 구독의 VNet 또는 다른 Azure Active Directory 테넌트에 속한 구독을 포함하여 다른 구독의 VNet에 속할 수 있습니다.

VNet 내의 Azure Storage에 대해 [서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하도록 설정합니다. 서비스 엔드포인트는 VNet의 트래픽을 Azure Storage 서비스에 대한 최적의 경로를 통해 라우팅합니다. 서브넷 및 가상 네트워크의 ID 또한 각 요청과 함께 전송됩니다. 그러면 관리자가 VNet의 특정 서브넷에서 요청을 받을 수 있도록 스토리지 계정에 대한 네트워크 규칙을 구성할 수 있습니다. 이러한 네트워크 규칙을 통해 액세스가 허용되는 클라이언트에서 데이터에 액세스하려면 스토리지 계정의 권한 부여 요구 사항을 계속 충족해야 합니다.

각 저장소 계정은 [IP 네트워크 규칙과](#grant-access-from-an-internet-ip-range)결합 될 수 있는 최대 200 가상 네트워크 규칙을 지원 합니다.

### <a name="available-virtual-network-regions"></a>사용 가능한 가상 네트워크 지역

일반적으로 서비스 엔드포인트는 동일한 Azure 지역의 가상 네트워크와 서비스 인스턴스 간에 작동합니다. Azure Storage에서 서비스 엔드포인트를 사용하는 경우 이 범위에는 [쌍을 이루는 지역](../../best-practices-availability-paired-regions.md)이 포함됩니다. 서비스 엔드포인트를 사용하면 지역별 장애 조치 및 읽기 전용 RA-GRS(지역 중복 스토리지) 인스턴스에 대한 액세스 중에 연속성을 유지할 수 있습니다. 가상 네트워크에서 스토리지 계정으로의 액세스를 허가하는 네트워크 규칙은 모든 RA-GRS 인스턴스에 대한 액세스도 허가합니다.

지역 가동 중단 시 재해 복구를 계획하는 경우 쌍을 이루는 지역에 VNet을 미리 만들어야 합니다. 이러한 대체 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙을 사용하여 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 그런 다음, 이러한 규칙을 지역 중복 스토리지 계정에 적용합니다.

> [!NOTE]
> 서비스 엔드포인트는 가상 네트워크 지역 및 지정된 지역 쌍 외부의 트래픽에는 적용되지 않습니다. 가상 네트워크에서 액세스할 수 있도록 허용하는 네트워크 규칙만 스토리지 계정의 주 지역 또는 쌍을 이루는 지정된 지역의 스토리지 계정에 적용할 수 있습니다.

### <a name="required-permissions"></a>필요한 사용 권한

가상 네트워크 규칙을 스토리지 계정에 적용하려면 추가되는 서브넷에 대한 적절한 권한이 사용자에게 있어야 합니다. 필요한 권한은 *서브넷에 서비스 조인* 이며, *스토리지 계정 기여자* 기본 제공 역할에 포함됩니다. 사용자 지정 역할 정의에 추가할 수도 있습니다.

스토리지 계정 및 가상 네트워크에 허용된 액세스 권한은 다른 구독(다른 Azure AD 테넌트의 일부인 구독 포함)에 있을 수 있습니다.

> [!NOTE]
> 다른 Azure Active Directory 테넌트에 속한 가상 네트워크의 서브넷에 대해 액세스 권한을 부여하는 규칙의 구성은 현재 Powershell, CLI 및 REST API를 통해서만 지원됩니다. 이러한 규칙은 Azure Portal에서 볼 수 있지만 구성할 수는 없습니다.

### <a name="managing-virtual-network-rules"></a>가상 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 가상 네트워크 규칙을 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라고 하는 설정 메뉴에서를 선택 합니다.

3. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

4. 새 네트워크 규칙을 사용 하 여 가상 네트워크에 대 한 액세스 권한을 부여 하려면 **가상** 네트워크에서 **기존 가상 네트워크 추가** 를 선택 하 고 **가상 네트워크** 및 **서브넷** 옵션을 선택한 다음 **추가** 를 선택 합니다. 새 가상 네트워크를 만들고 액세스 권한을 부여 하려면 **새 가상 네트워크 추가** 를 선택 합니다. 새 가상 네트워크를 만드는 데 필요한 정보를 제공 하 고 **만들기** 를 선택 합니다.

    > [!NOTE]
    > 이전에 Azure Storage에 대한 서비스 엔드포인트가 선택한 가상 네트워크 및 서브넷에 구성되지 않은 경우 이 작업의 일환으로 구성할 수 있습니다.
    >
    > 현재, 규칙을 만드는 동안 동일한 Azure Active Directory 테넌트에 속한 가상 네트워크만 선택할 수 있도록 표시됩니다. 다른 테넌트에 속한 가상 네트워크의 서브넷에 대한 액세스 권한을 부여하려면 Powershell, CLI 또는 REST API를 사용하세요.

5. 가상 네트워크 또는 서브넷 규칙을 제거 **하려면 ...을 선택 하** 여 가상 네트워크 또는 서브넷에 대 한 상황에 맞는 메뉴를 열고 **제거** 를 선택 합니다.

6. **저장** 을 선택 하 여 변경 내용을 적용 합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. 가상 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 네트워크 규칙을 추가하려면 "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name" 형식의 정규화된 **VirtualNetworkResourceId** 매개 변수를 사용합니다.

5. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

2. 가상 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. 기존 가상 네트워크 및 서브넷에서 Azure Storage에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > 다른 Azure AD 테 넌 트에 속한 VNet의 서브넷에 대 한 규칙을 추가 하려면 "/subscriptions/ \<subscription-ID\> /Sggg//providers/Microsoft.Network/virtualNetworks//subnets/" 형식으로 정규화 된 서브넷 ID를 사용 \<resourceGroup-Name\> \<vNet-name\> \<subnet-name\> 합니다.
    >
    > **subscription** 매개 변수를 사용하여 다른 Azure AD 테넌트에 속한 VNet의 서브넷 ID를 검색할 수 있습니다.

5. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

---

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가

특정 공용 인터넷 IP 주소 범위에서 액세스할 수 있도록 스토리지 계정을 구성할 수 있습니다. 이 구성은 특정 인터넷 기반 서비스와 온-프레미스 네트워크에 대한 액세스 권한을 부여하고 일반 인터넷 트래픽을 차단합니다.

*16.17.18.0/24* 형식의 [CIDR 표기법](https://tools.ietf.org/html/rfc4632)을 사용하거나 개별 IP 주소(예: *16.17.18.19*)를 사용하여 허용된 인터넷 주소 범위를 제공합니다.

   > [!NOTE]
   > "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.

IP 네트워크 규칙은 **공용 인터넷** IP 주소에 대해서만 허용됩니다. 사설망에 예약된 IP 주소 범위([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)에 정의된 대로)는 IP 규칙에서 허용되지 않습니다. 사설망에는 _10.*_ , _172.16.*_  - _172.31.*_ 및 _192.168.*_ 로 시작하는 주소가 포함됩니다.

   > [!NOTE]
   > IP 네트워크 규칙은 스토리지 계정과 동일한 Azure 지역에서 시작된 요청에 영향을 주지 않습니다. 동일한 지역 요청을 허용하려면 [가상 네트워크 규칙](#grant-access-from-a-virtual-network)을 사용하세요.

  > [!NOTE]
  > 스토리지 계정과 동일한 지역에 배포된 서비스는 통신을 위해 프라이빗 Azure IP 주소를 사용합니다. 따라서 퍼블릭 아웃바운드 IP 주소 범위에 따라 특정 Azure 서비스에 대한 액세스를 제한할 수 없습니다.

스토리지 방화벽 규칙의 구성에는 IPV4 주소만 지원됩니다.

각 저장소 계정은 최대 200 IP 네트워크 규칙을 지원 합니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성

IP 네트워크 규칙을 사용하여 온-프레미스 네트워크에서 스토리지 계정으로의 액세스 권한을 부여하려면 네트워크에서 사용되는 인터넷 연결 IP 주소를 식별해야 합니다. 네트워크 관리자에게 도움을 요청합니다.

공용 피어링 또는 Microsoft 피어링을 위해 온-프레미스에서 [ExpressRoute](../../expressroute/expressroute-introduction.md)를 사용하는 경우 사용되는 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리

Azure Portal, PowerShell 또는 CLIv2를 통해 스토리지 계정에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라고 하는 설정 메뉴에서를 선택 합니다.

3. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

4. 인터넷 IP 범위에 대한 액세스 권한을 부여하려면 **방화벽** > **주소 범위** 아래에서 IP 주소 또는 주소 범위(CIDR 형식)를 입력합니다.

5. IP 네트워크 규칙을 제거 하려면 주소 범위 옆에 있는 휴지통 아이콘을 선택 합니다.

6. **저장** 을 선택하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. IP 네트워크 규칙을 나열합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

1. IP 네트워크 규칙을 나열합니다.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Azure 리소스 인스턴스에서 액세스 권한 부여 (미리 보기)

경우에 따라 응용 프로그램은 가상 네트워크 또는 IP 주소 규칙을 통해 격리할 수 없는 Azure 리소스에 종속 될 수 있습니다. 그러나 응용 프로그램의 Azure 리소스에 대해서만 저장소 계정 액세스를 보호 하 고 제한 하는 것도 좋습니다. 리소스 인스턴스 규칙을 만들어 일부 Azure 서비스의 특정 리소스 인스턴스에 대 한 액세스를 허용 하도록 저장소 계정을 구성할 수 있습니다. 

리소스 인스턴스가 저장소 계정 데이터에서 수행할 수 있는 작업의 유형은 리소스 인스턴스의 [Azure 역할 할당](storage-auth-aad.md#assign-azure-roles-for-access-rights) 에 의해 결정 됩니다. 리소스 인스턴스는 저장소 계정과 동일한 테 넌 트에 있어야 하지만 테 넌 트의 모든 구독에 속할 수 있습니다.

지원 되는 Azure 서비스 목록은이 문서의 [시스템 할당 관리 id를 기반으로](#trusted-access-system-assigned-managed-identity) 하는 트러스트 된 액세스 섹션에 표시 됩니다.

> [!NOTE]
> 이 기능은 공개 미리 보기 상태 이며 모든 공용 클라우드 지역에서 사용할 수 있습니다. 

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 리소스 네트워크 규칙을 추가 하거나 제거할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. 네트워킹 **을 선택 하** 여 네트워킹의 구성 페이지를 표시 합니다.

4. **리소스 종류** 드롭다운 목록에서 리소스 인스턴스의 리소스 종류를 선택 합니다. 

5. **인스턴스 이름** 드롭다운 목록에서 리소스 인스턴스를 선택 합니다. 활성 테 넌 트, 구독 또는 리소스 그룹에 모든 리소스 인스턴스를 포함 하도록 선택할 수도 있습니다.

6. **저장** 을 선택하여 변경 내용을 적용합니다. 리소스 인스턴스는 네트워크 설정 페이지의 **리소스 인스턴스** 섹션에 표시 됩니다. 

리소스 인스턴스를 제거 하려면 :::image type="icon" source="media/storage-network-security/delete-icon.png"::: 리소스 인스턴스 옆에 있는 삭제 아이콘 ()을 선택 합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령을 사용 하 여 리소스 네트워크 규칙을 추가 하거나 제거할 수 있습니다.

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

#### <a name="install-the-preview-module"></a>미리 보기 모듈 설치

PowershellGet 모듈의 최신 버전을 설치 합니다. 그런 다음 PowerShell 콘솔을 닫았다가 다시 엽니다.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

설치 **Az. Storage** preview module.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

PowerShell 모듈을 설치 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps) 를 참조 하세요.

#### <a name="grant-access"></a>액세스 권한 부여

리소스 인스턴스에 대 한 액세스 권한을 부여 하는 네트워크 규칙을 추가 합니다.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

네트워크 규칙 집합을 수정 하 여 한 번에 여러 리소스 인스턴스를 지정 합니다.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>액세스 권한 제거

리소스 인스턴스에 대 한 액세스 권한을 부여 하는 네트워크 규칙을 제거 합니다.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

리소스 인스턴스에서 액세스 권한을 부여 하는 모든 네트워크 규칙을 제거 합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>허용 되는 리소스 인스턴스 목록 보기

저장소 계정에 대 한 액세스 권한이 부여 된 리소스 인스턴스의 전체 목록을 표시 합니다.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 명령을 사용 하 여 리소스 네트워크 규칙을 추가 하거나 제거할 수 있습니다.

#### <a name="install-the-preview-extension"></a>Preview 확장 설치

1. [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. 그런 다음, `2.13.0` 다음 명령을 사용 하 여 설치한 Azure CLI 버전이 이상 인지 확인 합니다.

   ```azurecli
   az --version
   ```

   Azure CLI 버전이 `2.13.0`보다 낮은 경우 이후 버전을 설치합니다. [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

3. 다음 명령을 입력 하 여 미리 보기 확장을 설치 합니다.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>액세스 권한 부여

리소스 인스턴스에 대 한 액세스 권한을 부여 하는 네트워크 규칙을 추가 합니다.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>액세스 권한 제거

리소스 인스턴스에 대 한 액세스 권한을 부여 하는 네트워크 규칙을 제거 합니다.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>허용 되는 리소스 인스턴스 목록 보기

저장소 계정에 대 한 액세스 권한이 부여 된 리소스 인스턴스의 전체 목록을 표시 합니다.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-azure-services"></a>Azure 서비스에 대 한 액세스 권한 부여 

일부 Azure 서비스는 네트워크 규칙에 포함할 수 없는 네트워크에서 작동 합니다. 이러한 신뢰할 수 있는 Azure 서비스의 하위 집합을 저장소 계정에 부여 하 고 다른 앱에 대 한 네트워크 규칙을 유지 관리할 수 있습니다. 이러한 신뢰할 수 있는 서비스는 강력한 인증을 사용 하 여 저장소 계정에 안전 하 게 연결 합니다. 

네트워크 규칙 예외를 만들어 신뢰할 수 있는 Azure 서비스에 대 한 액세스 권한을 부여할 수 있습니다. 단계별 지침은이 문서의 [예외 관리](#manage-exceptions) 섹션을 참조 하세요. 

신뢰할 수 있는 Azure 서비스에 대 한 액세스 권한을 부여 하는 경우 다음 유형의 액세스를 부여 합니다.

- 구독에 등록 된 리소스에 대 한 선택 작업에 대 한 트러스트 된 액세스입니다.
- 시스템 할당 관리 id를 기반으로 하는 리소스에 대 한 신뢰할 수 있는 액세스

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>구독에 등록 된 리소스에 대 한 신뢰할 수 있는 액세스

**구독에 등록된 경우** 일부 서비스의 리소스는 로그 또는 백업과 같은 선택 작업에 대해 **동일한 구독** 에 있는 스토리지 계정에 액세스할 수 있습니다.  다음 표에서는 각 서비스 및 허용 되는 작업에 대해 설명 합니다. 

| 서비스                  | 리소스 공급자 이름     | 허용되는 연산                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | IAAS 가상 머신에서 관리되지 않는 디스크의 백업 및 복원을 실행합니다. (관리되는 디스크에 필요 없음). [자세히 알아보기](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft.DataBox          | Data Box를 사용하여 Azure로 데이터를 가져올 수 있습니다. [자세히 알아보기](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | 사용자 지정 이미지 만들기 및 아티팩트 설치. [자세히 알아보기](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft.EventGrid        | Blob Storage 이벤트 게시를 사용하도록 설정하고 Event Grid가 스토리지 큐에 게시하도록 허용합니다. [Blob Storage 이벤트](../../event-grid/overview.md#event-sources)와 [큐에 게시](../../event-grid/event-handlers.md)에 대해 알아봅니다. |
| Azure Event Hubs         | Microsoft.EventHub         | Event Hubs 캡처로 데이터를 보관합니다. [자세한 정보](../../event-hubs/event-hubs-capture-overview.md). |
| Azure 파일 동기화          | Microsoft.StorageSync      | Azure 파일 공유를 위해 온-프레미스 파일 서버를 캐시로 변환할 수 있습니다. 다중 사이트 동기화, 빠른 재해 복구 및 클라우드 쪽 백업을 허용합니다. [자세히 알아보기](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | 새 HDInsight 클러스터에 대한 기본 파일 시스템의 초기 콘텐츠를 프로비저닝합니다. [자세히 알아보기](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Azure Import/Export      | Microsoft.ImportExport     | Azure Storage Import/Export 서비스를 사용 하 여 Azure Storage 데이터를 Azure Storage 하거나 내보낼 데이터를 가져올 수 있습니다. [자세히 알아보기](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft.Insights         | 리소스 로그 Azure Active Directory 로그인 및 감사 로그, Microsoft Intune 로그를 포함하는 모니터링 데이터를 보안 스토리지 계정에 쓸 수 있습니다. [자세히 알아보기](../../azure-monitor/platform/roles-permissions-security.md). |
| Azure 네트워킹         | Microsoft.Network          | Network Watcher 및 트래픽 분석 서비스를 비롯 하 여 네트워크 트래픽 로그를 저장 하 고 분석 합니다. [자세히 알아보기](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | 방화벽 지원 캐시, 원본 또는 대상 스토리지 계정을 사용하는 경우 Azure IaaS 가상 머신의 재해 복구를 위해 복제를 사용하도록 설정합니다.  [자세히 알아보기](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>시스템이 할당 한 관리 되는 id를 기반으로 하는 신뢰할 수 있는 액세스

다음 표에서는 해당 서비스의 리소스 인스턴스에 적절 한 권한을 부여 하는 경우 저장소 계정 데이터에 액세스할 수 있는 서비스를 나열 합니다. 권한을 부여 하려면 각 리소스 인스턴스에 대해 [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 에 [Azure 역할](storage-auth-aad.md#assign-azure-roles-for-access-rights) 을 명시적으로 할당 해야 합니다. 이 경우 인스턴스에 대한 액세스 범위는 관리 ID에 할당된 Azure 역할에 해당합니다. 

> [!TIP]
> 특정 리소스에 대 한 액세스 권한을 부여 하는 권장 방법은 리소스 인스턴스 규칙을 사용 하는 것입니다. 특정 리소스 인스턴스에 대 한 액세스 권한을 부여 하려면이 문서의 [Azure 리소스 인스턴스에 대 한 액세스 권한 부여 (미리 보기)](#grant-access-specific-instances) 섹션을 참조 하세요.


| 서비스                        | 리소스 공급자 이름                 | 목적            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | 정책을 사용 하 여 방화벽 뒤에 있는 저장소 계정에 대 한 Api Management 서비스 액세스를 활성화 합니다. [자세한 정보를 알아보세요](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Cognitive Search 서비스를 사용하여 인덱싱, 처리 및 쿼리를 위해 스토리지 계정에 액세스할 수 있습니다. |
| Azure Cognitive Services       | CognitiveService             | Cognitive Services에서 저장소 계정에 액세스할 수 있습니다. |
| Azure Container Registry 작업 | Microsoft.ContainerRegistry/registries | ACR 태스크는 컨테이너 이미지를 빌드할 때 스토리지 계정에 액세스할 수 있습니다. |
| Azure 데이터 팩터리             | Microsoft.DataFactory/factories        | ADF 런타임을 통해 스토리지 계정에 액세스할 수 있도록 합니다. |
| Azure Data Share               | Microsoft.DataShare/accounts           | Data Share를 통해 스토리지 계정에 액세스할 수 있도록 합니다. |
| Azure IoT Hub                  | Microsoft.Devices/IotHubs              | IoT Hub의 데이터를 Blob 스토리지에 쓸 수 있습니다. [자세히 알아보기](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft.Logic/workflows              | 논리 앱을 사용하여 스토리지 계정에 액세스할 수 있습니다. [자세히 알아보기](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning 서비스 | Microsoft.MachineLearningServices      | 권한 있는 Azure Machine Learning 작업 영역은 실험 출력, 모델 및 로그를 Blob 스토리지에 쓰고 데이터를 읽습니다. [자세히 알아보기](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). | 
| Azure Synapse Analytics       | Microsoft.Sql                          | COPY 문이나 PolyBase (전용 풀) 또는 `openrowset` 서버 리스 풀의 함수 및 외부 테이블을 사용 하 여 특정 SQL 데이터베이스에서 데이터를 가져오거나 내보낼 수 있습니다. [자세한 정보를 알아보세요](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure SQL Database       | Microsoft.Sql                          | 방화벽 뒤에 있는 저장소 계정에 감사 데이터를 [쓸](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) 수 있습니다. |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | 스트리밍 작업의 데이터를 Blob 스토리지에 쓸 수 있습니다. [자세히 알아보기](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft.Synapse/workspaces          | Azure Synapse Analytics에서 Azure Storage의 데이터에 액세스할 수 있습니다. |

## <a name="grant-access-to-storage-analytics"></a>저장소 분석에 대 한 액세스 권한 부여

경우에 따라 네트워크 경계 밖에서 리소스 로그 및 메트릭을 읽을 수 있는 권한이 필요합니다. 저장소 계정에 대 한 신뢰할 수 있는 서비스 액세스를 구성할 때 네트워크 규칙 예외를 만들어 로그 파일, 메트릭 테이블 또는 둘 모두에 대 한 읽기 액세스를 허용할 수 있습니다. 단계별 지침은 아래의 **예외 관리** 섹션을 참조 하세요. 저장소 분석 작업에 대 한 자세한 내용은 [Azure Storage analytics를 사용 하 여 로그 및 메트릭 데이터 수집](./storage-analytics.md)을 참조 하세요. 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>예외 관리

Azure Portal, PowerShell 또는 Azure CLI v2를 통해 네트워크 규칙 예외를 관리할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 보호하려는 스토리지 계정으로 이동합니다.

2. **네트워킹** 이라고 하는 설정 메뉴에서를 선택 합니다.

3. **선택한 네트워크** 에서 액세스를 허용하도록 선택했는지 확인합니다.

4. **예외** 아래에서 허용하려는 예외를 선택합니다.

5. **저장** 을 선택하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Azure PowerShell](/powershell/azure/install-Az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.

2. 스토리지 계정 네트워크 규칙에 대한 예외를 표시합니다.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. 스토리지 계정 네트워크 규칙에 대한 예외를 구성합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. 스토리지 계정 네트워크 규칙에 대한 예외를 제거합니다.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부** 로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 효과가 없습니다.

---

## <a name="next-steps"></a>다음 단계

[서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)에서 Azure 네트워크 서비스 엔드포인트에 대해 자세히 알아봅니다.

[Azure Storage 보안 가이드](../blobs/security-recommendations.md)에서 Azure Storage 보안에 대해 자세히 살펴봅니다.
