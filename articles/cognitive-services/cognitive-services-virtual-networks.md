---
title: Virtual Network
titleSuffix: Azure Cognitive Services
description: 코그너티브 서비스 리소스에 대해 계층화된 네트워크 보안을 구성합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371225"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Azure 코그너티브 서비스 가상 네트워크 구성

Azure Cognitive 서비스는 계층화된 보안 모델을 제공합니다. 이 모델을 사용하면 코그너티브 서비스 계정을 특정 네트워크 하위 집합에 보호할 수 있습니다. 네트워크 규칙이 구성되면 지정된 네트워크 집합을 통해 데이터를 요청하는 응용 프로그램만 계정에 액세스할 수 있습니다. 요청 필터링을 사용하여 리소스에 대한 액세스를 제한할 수 있습니다. 지정된 IP 주소, IP 범위 또는 [Azure 가상 네트워크의](../virtual-network/virtual-networks-overview.md)서브넷 목록에서 시작된 요청만 허용합니다. 이 오퍼링에 관심이 있으시면 [미리 보기 액세스를 요청해야](https://aka.ms/cog-svc-vnet-signup)합니다.

네트워크 규칙이 적용될 때 코그너티브 서비스 리소스에 액세스하는 응용 프로그램에는 권한 부여가 필요합니다. 권한 부여는 [Azure Active Directory(Azure](../active-directory/fundamentals/active-directory-whatis.md) AD) 자격 증명 또는 유효한 API 키로 지원됩니다.

> [!IMPORTANT]
> Cognitive Services 계정에 대한 방화벽 규칙을 켜면 기본적으로 들어오는 데이터 요청을 차단합니다. 요청을 통과하려면 다음 조건 중 하나를 충족해야 합니다.
> * 요청은 대상 인지 서비스 계정의 허용된 서브넷 목록에서 Azure 가상 네트워크(VNet) 내에서 작동하는 서비스에서 시작되어야 합니다. VNet에서 시작된 요청의 끝점은 코그너티브 서비스 계정의 [사용자 지정 하위 도메인으로](cognitive-services-custom-subdomains.md) 설정해야 합니다.
> * 또는 요청은 허용된 IP 주소 목록에서 시작해야 합니다.
>
> 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>시나리오

코그너티브 서비스 리소스를 보호하려면 기본적으로 모든 네트워크(인터넷 트래픽 포함)의 트래픽 에 대한 액세스를 거부하는 규칙을 구성해야 합니다. 그런 다음 특정 VNet의 트래픽에 대한 액세스 권한을 부여하는 규칙을 구성해야 합니다. 이 구성을 사용하면 애플리케이션에 대한 보안 네트워크 경계를 구축할 수 있습니다. 또한 일부 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여하도록 규칙을 구성하여 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 활성화할 수도 있습니다.

네트워크 규칙은 REST 및 WebSocket을 포함한 Azure 인지 서비스에 대한 모든 네트워크 프로토콜에 적용됩니다. Azure 테스트 콘솔과 같은 도구를 사용하여 데이터에 액세스하려면 명시적 네트워크 규칙을 구성해야 합니다. 기존 코그너티브 서비스 리소스에 네트워크 규칙을 적용하거나 새 코그너티브 서비스 리소스를 만들 때 적용할 수 있습니다. 네트워크 규칙이 적용되면 모든 요청에 적용됩니다.

## <a name="supported-regions-and-service-offerings"></a>지원되는 지역 및 서비스 제공

아래에 나열된 코그너티브 서비스에 대한 가상 네트워크 지원은 *미국 중부 EUAP,* *미국 중남부,* *미국 동부,* *미국 서부 2,* *북유럽,* *남아프리카 북부,* *서유럽,* *인도 중부,* *오스트레일리아 동부,* *미국 서부*및 *미국 버지니아* 주 정부 지역으로 제한됩니다. 서비스 제공이 여기에 나열되지 않으면 가상 네트워크를 지원하지 않습니다.

> [!div class="checklist"]
> * [Anomaly Detector](./anomaly-detector/index.yml)
> * [컴퓨터 비전](./computer-vision/index.yml)
> * [콘텐츠 진행자](./content-moderator/index.yml)
> * [맞춤형 비전](./custom-vision-service/index.yml)
> * [얼굴](./face/index.yml)
> * [Form Recognizer](./form-recognizer/index.yml)
> * [루이스](./luis/index.yml)
> * [개인화](./personalizer/index.yml)
> * [텍스트 분석](./text-analytics/index.yml)
> * [QnA 메이커](./qnamaker/index.yml)

아래에 나열된 코그너티브 서비스에 대한 가상 네트워크 지원은 *미국 중부 EUAP,* *미국 중남부,* *미국 동부,* *미국 서부 2,* *글로벌*및 미국 *버지니아 주* 정부 Azure 지역으로 제한됩니다.
> [!div class="checklist"]
> * [번역기 텍스트](./translator/index.yml)

## <a name="service-tags"></a>서비스 태그
코그너티브 서비스는 위의 서비스에 대한 가상 네트워크 서비스 엔드포인트를 지원하는 것 외에도 아웃바운드 네트워크 규칙 구성에 대한 서비스 태그도 지원합니다. 다음 서비스는 CognitiveServicesManagement 서비스 태그에 포함됩니다.
> [!div class="checklist"]
> * [Anomaly Detector](./anomaly-detector/index.yml)
> * [컴퓨터 비전](./computer-vision/index.yml)
> * [콘텐츠 진행자](./content-moderator/index.yml)
> * [맞춤형 비전](./custom-vision-service/index.yml)
> * [얼굴](./face/index.yml)
> * [Form Recognizer](./form-recognizer/index.yml)
> * [루이스](./luis/index.yml)
> * [개인화](./personalizer/index.yml)
> * [텍스트 분석](./text-analytics/index.yml)
> * [QnA 메이커](./qnamaker/index.yml)
> * [번역기 텍스트](./translator/index.yml)
> * [Speech Service](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경

기본적으로 Cognitive Services 리소스는 모든 네트워크의 클라이언트연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경하면 응용 프로그램이 Azure Cognitive 서비스에 연결하는 기능에 영향을 미칠 수 있습니다. 기본 네트워크 규칙을 **거부하도록** 설정하면 액세스 **권한을 부여하는** 특정 네트워크 규칙도 적용되지 않는 한 데이터에 대한 모든 액세스가 차단됩니다. 액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다. 온-프레미스 네트워크에 대한 IP 주소를 나열하도록 허용하는 경우 온-프레미스 네트워크에서 가능한 모든 공개 IP 주소를 추가해야 합니다.

### <a name="managing-default-network-access-rules"></a>기본 네트워크 액세스 규칙 관리

Azure 포털, PowerShell 또는 Azure CLI를 통해 코그너티브 서비스 리소스에 대한 기본 네트워크 액세스 규칙을 관리할 수 있습니다.

# <a name="azure-portal"></a>[Azure 포털](#tab/portal)

1. 보안하려는 코그너티브 서비스 리소스로 이동합니다.

1. **가상 네트워크라는** **리소스 관리** 메뉴를 선택합니다.

   ![가상 네트워크 옵션](media/vnet/virtual-network-blade.png)

1. 기본적으로 액세스를 거부하려면 **선택한 네트워크**에서 액세스를 허용하도록 선택합니다. 선택한 **네트워크** 설정만 으로, 구성 된 **가상 네트워크** 또는 **주소 범위에** 의해 동반 하지-모든 액세스는 효과적으로 거부 됩니다. 모든 액세스가 거부되면 코그너티브 서비스 리소스를 사용하려는 요청은 허용되지 않습니다. Azure 포털, Azure PowerShell 또는 Azure CLI를 사용하여 인지 서비스 리소스를 구성할 수 있습니다.
1. 모든 네트워크의 트래픽을 허용하려면 **모든 네트워크**에서 액세스를 허용하도록 선택합니다.

   ![가상 네트워크 거부](media/vnet/virtual-network-deny.png)

1. **저장**을 선택하여 변경 내용을 적용합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Azure [PowerShell을](/powershell/azure/install-az-ps) 설치하고 [로그인하거나](/powershell/azure/authenticate-azureps) **을 선택합니다.**

1. 코그너티브 서비스 리소스에 대한 기본 규칙의 상태를 표시합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure [CLI를](/cli/azure/install-azure-cli) 설치하고 [로그인하거나](/cli/azure/authenticate-azure-cli) **시도를 선택합니다.**

1. 코그너티브 서비스 리소스에 대한 기본 규칙의 상태를 표시합니다.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가

특정 서브넷에서만 액세스를 허용하도록 코그너티브 서비스 리소스를 구성할 수 있습니다. 허용되는 서브넷은 동일한 구독의 VNet 또는 다른 Azure Active Directory 테넌트에 속한 구독을 포함하여 다른 구독에 속할 수 있습니다.

VNet 내에서 Azure 인지 서비스에 대한 [서비스 끝점을](../virtual-network/virtual-network-service-endpoints-overview.md) 활성화합니다. 서비스 끝점은 VNet에서 Azure 인지 서비스 서비스에 대한 최적의 경로를 통해 트래픽을 라우팅합니다. 서브넷과 가상 네트워크의 ID도 각 요청과 함께 전송됩니다. 그런 다음 관리자는 VNet의 특정 서브넷에서 요청을 받을 수 있도록 하는 Cognitive Services 리소스에 대한 네트워크 규칙을 구성할 수 있습니다. 이러한 네트워크 규칙을 통해 액세스 권한을 부여받은 클라이언트는 데이터에 액세스하려면 Cognitive Services 리소스의 권한 부여 요구 사항을 계속 충족해야 합니다.

각 코그너티브 서비스 리소스는 [IP 네트워크](#grant-access-from-an-internet-ip-range)규칙과 결합될 수 있는 최대 100개의 가상 네트워크 규칙을 지원합니다.

### <a name="required-permissions"></a>필요한 사용 권한

인지 서비스 리소스에 가상 네트워크 규칙을 적용하려면 추가되는 서브넷에 대한 적절한 권한이 있어야 합니다. 필요한 권한은 기본 *기여자* 역할 또는 *코그너티브 서비스 기여자* 역할입니다. 필수 사용 권한은 사용자 지정 역할 정의에 추가할 수도 있습니다.

코그너티브 서비스 리소스 및 액세스 권한을 부여한 가상 네트워크는 다른 Azure AD 테넌트의 일부인 구독을 포함하여 다른 구독에 있을 수 있습니다.

> [!NOTE]
> 다른 Azure Active Directory 테넌트의 일부인 가상 네트워크의 서브넷에 대한 액세스 권한을 부여하는 규칙의 구성은 현재 Powershell, CLI 및 REST API를 통해서만 지원됩니다. 이러한 규칙은 Azure 포털을 통해 구성할 수 없지만 포털에서 볼 수 있습니다.

### <a name="managing-virtual-network-rules"></a>가상 네트워크 규칙 관리

Azure 포털, PowerShell 또는 Azure CLI를 통해 코그너티브 서비스 리소스에 대한 가상 네트워크 규칙을 관리할 수 있습니다.

# <a name="azure-portal"></a>[Azure 포털](#tab/portal)

1. 보안하려는 코그너티브 서비스 리소스로 이동합니다.

1. **가상 네트워크라는** **리소스 관리** 메뉴를 선택합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 기존 네트워크 규칙이 있는 가상 네트워크에 대한 액세스 권한을 부여하려면 **가상 네트워크에서**기존 가상 **네트워크 추가를**선택합니다.

   ![기존 vNet 추가](media/vnet/virtual-network-add-existing.png)

1. 가상 **네트워크** 및 **서브넷** 옵션을 선택한 다음 **에서 를**선택합니다.

   ![기존 vNet 세부 정보 추가](media/vnet/virtual-network-add-existing-details.png)

1. 새 가상 네트워크를 만들고 액세스 권한을 부여하려면 **새 가상 네트워크 추가를**선택합니다.

   ![새 vNet 추가](media/vnet/virtual-network-add-new.png)

1. 새 가상 네트워크를 만드는 데 필요한 정보를 제공한 다음 **에서 만들기를**선택합니다.

   ![vNet 만들기](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Azure Cognitive Services에 대한 서비스 끝점이 이전에 선택한 가상 네트워크 및 서브넷에 대해 구성되지 않은 경우 이 작업의 일부로 구성할 수 있습니다.
    >
    > 현재 는 규칙을 만드는 동안 선택을 위해 동일한 Azure Active Directory 테넌트에 속한 가상 네트워크만 표시됩니다. 다른 테넌트에 속한 가상 네트워크의 서브넷에 대한 액세스 권한을 부여하려면 Powershell, CLI 또는 REST API를 사용하십시오.

1. 가상 네트워크 또는 서브넷 규칙을 제거하려면 **가상** 네트워크 또는 서브넷의 컨텍스트 메뉴를 열려고 선택하고 **제거를**선택합니다.

   ![vNet 제거](media/vnet/virtual-network-remove.png)

1. **저장**을 선택하여 변경 내용을 적용합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Azure [PowerShell을](/powershell/azure/install-az-ps) 설치하고 [로그인하거나](/powershell/azure/authenticate-azureps) **을 선택합니다.**

1. 가상 네트워크 규칙을 나열합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Cognitive 서비스에 대한 서비스 엔드포인트를 활성화합니다.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 네트워크 규칙을 추가하려면 "/구독/구독-ID/리소스 그룹/리소스 그룹/공급자/Microsoft.Network/vNet-name/서브넷 이름" 양식에 정규화된 **VirtualNetworkResourceId** 매개 변수를 사용합니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure [CLI를](/cli/azure/install-azure-cli) 설치하고 [로그인하거나](/cli/azure/authenticate-azure-cli) **시도를 선택합니다.**

1. 가상 네트워크 규칙을 나열합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. 기존 가상 네트워크 및 서브넷에서 Azure Cognitive 서비스에 대한 서비스 엔드포인트를 활성화합니다.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > 다른 Azure AD 테넌트에 속한 VNet의 서브넷에 대한 규칙을 추가하려면 "/구독/구독 ID/리소스 그룹/리소스 그룹/공급자/Microsoft.Network/vNet-name/서브넷 이름" 형식으로 정규화된 서브넷 ID를 사용합니다.
    > 
    > **구독** 매개 변수를 사용하여 다른 Azure AD 테넌트에 속한 VNet에 대한 서브넷 ID를 검색할 수 있습니다.

1. 가상 네트워크 및 서브넷에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가

특정 공용 인터넷 IP 주소 범위에서 액세스할 수 있도록 코그너티브 서비스 리소스를 구성할 수 있습니다. 이 구성은 특정 서비스 및 온-프레미스 네트워크에 대한 액세스를 부여하여 일반 인터넷 트래픽을 효과적으로 차단합니다.

`16.17.18.0/24` `16.17.18.19` [형식의 CIDR 표기와](https://tools.ietf.org/html/rfc4632) 같은 개별 IP 주소로 허용되는 인터넷 주소 범위를 제공합니다.

   > [!Tip]
   > "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다. 이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.

IP 네트워크 규칙은 **공용 인터넷** IP 주소에 대해서만 허용됩니다. 사설망에 예약된 IP 주소 범위([RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)에 정의된 대로)는 IP 규칙에서 허용되지 않습니다. 개인 `10.*`네트워크에는 에서 `172.16.*`  -  `172.31.*`로 시작하는 `192.168.*`주소가 포함됩니다.

   > [!NOTE]
   > IP 네트워크 규칙은 코그너티브 서비스 리소스와 동일한 Azure 리전에서 시작된 요청에 영향을 주지 않습니다. 동일한 지역 요청을 허용하려면 [가상 네트워크 규칙](#grant-access-from-a-virtual-network)을 사용하세요.

현재 IPv4 주소만 지원됩니다. 각 코그너티브 서비스 리소스는 최대 100개의 IP 네트워크 규칙을 지원하며, 이 규칙은 [가상 네트워크 규칙과](#grant-access-from-a-virtual-network)결합될 수 있습니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성

IP 네트워크 규칙을 사용하여 온-프레미스 네트워크에서 코그너티브 서비스 리소스에 대한 액세스 권한을 부여하려면 네트워크에서 사용하는 인터넷 연결 IP 주소를 식별해야 합니다. 네트워크 관리자에게 도움을 요청합니다.

공용 피어링 또는 Microsoft 피어링을 위해 [ExpressRoute](../expressroute/expressroute-introduction.md) 온-프레미스를 사용하는 경우 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 두 개의 NAT IP 주소를 사용합니다. 트래픽이 Microsoft Azure 네트워크 백본에 들어갈 때 각각 Azure 서비스 트래픽에 적용됩니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. 공용 피어링 ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리

Azure 포털, PowerShell 또는 Azure CLI를 통해 인지 서비스 리소스에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

# <a name="azure-portal"></a>[Azure 포털](#tab/portal)

1. 보안하려는 코그너티브 서비스 리소스로 이동합니다.

1. **가상 네트워크라는** **리소스 관리** 메뉴를 선택합니다.

1. **선택한 네트워크**에서 액세스를 허용하도록 선택했는지 확인합니다.

1. 인터넷 IP 범위에 대한 액세스 권한을 부여하려면 **방화벽** > 주소 범위 아래에 IP 주소 또는 주소 범위(CIDR [형식)를](https://tools.ietf.org/html/rfc4632)**입력합니다.** 유효한 공용 IP(예약되지 않은) 주소만 허용됩니다.

   ![IP 범위 추가](media/vnet/virtual-network-add-ip-range.png)

1. IP 네트워크 규칙을 제거하려면 주소 <span class="docon docon-delete x-hidden-focus"></span> 범위 옆에 있는 휴지통 아이콘을 선택합니다.

   ![IP 범위 삭제](media/vnet/virtual-network-delete-ip-range.png)

1. **저장**을 선택하여 변경 내용을 적용합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Azure [PowerShell을](/powershell/azure/install-az-ps) 설치하고 [로그인하거나](/powershell/azure/authenticate-azureps) **을 선택합니다.**

1. IP 네트워크 규칙을 나열합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure [CLI를](/cli/azure/install-azure-cli) 설치하고 [로그인하거나](/cli/azure/authenticate-azure-cli) **시도를 선택합니다.**

1. IP 네트워크 규칙을 나열합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 **거부**로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

* 다양한 [Azure 코그너티브 서비스](welcome.md) 살펴보기
* [Azure 가상 네트워크 서비스 엔드포인트에](../virtual-network/virtual-network-service-endpoints-overview.md) 대해 자세히 알아보기