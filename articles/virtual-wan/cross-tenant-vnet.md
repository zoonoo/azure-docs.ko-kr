---
title: '허브에 교차 테 넌 트 Vnet 연결: PowerShell'
titleSuffix: Azure Virtual WAN
description: 이 문서는 PowerShell을 사용 하 여 테 넌 트 간 Vnet를 가상 허브에 연결 하는 데 도움이 됩니다.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: c49a85c71c9b877be7e143f5caf27dc307fe0c12
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381272"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>가상 Wan 허브에 교차 테 넌 트 Vnet 연결

이 문서는 가상 WAN을 사용 하 여 다른 테 넌 트의 가상 허브에 VNet을 연결 하는 데 도움이 됩니다. 이 아키텍처는 같은 네트워크에 연결 되어야 하지만 다른 테 넌 트에 있는 클라이언트 작업을 포함 하는 경우에 유용 합니다. 예를 들어 다음 다이어그램에 표시 된 것 처럼 contoso VNet (원격 테 넌 트)을 Contoso 가상 허브 (부모 테 넌 트)에 연결할 수 있습니다.

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="라우팅 구성 설정" :::

이 문서에서는 다음 방법을 설명합니다.

* 다른 테 넌 트를 Azure 구독에 참가자로 추가 합니다.
* 가상 허브에 교차 테 넌 트 VNet을 연결 합니다.

이 구성에 대 한 단계는 Azure Portal와 PowerShell의 조합을 사용 하 여 수행 됩니다. 그러나 기능 자체는 PowerShell 및 CLI 에서만 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 사용 하려면 환경에서 다음 구성이 이미 설정 되어 있어야 합니다.

* 부모 구독의 가상 WAN 및 가상 허브.
* 다른 (원격) 테 넌 트의 구독에 구성 된 가상 네트워크입니다.
* 원격 테 넌 트의 VNet 주소 공간이 이미 부모 가상 허브에 연결 된 다른 Vnet 내의 다른 주소 공간과 겹치지 않는지 확인 합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>권한 할당

가상 허브를 사용 하 여 부모 구독에서 원격 테 넌 트의 가상 네트워크를 수정 하 고 액세스할 수 있도록 하려면 원격 테 넌 트 구독에서 부모 구독에 대 한 **참가자** 권한을 할당 해야 합니다.

1. 부모 계정 (가상 WAN 허브를 사용 하는 계정)에 **참가자** 역할 할당을 추가 합니다. PowerShell 또는 Azure Portal를 사용 하 여이 역할을 할당할 수 있습니다. 단계는 다음 **역할 할당 추가 또는 제거** 문서를 참조 하세요.

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [포털](../role-based-access-control/role-assignments-portal.md)

1. 그런 다음 PowerShell의 현재 세션에 원격 테 넌 트 구독과 부모 테 넌 트 구독을 추가 합니다. 다음 명령을 실행합니다. 부모에 로그인 한 경우에는 원격 테 넌 트에 대해서만 명령을 실행 하면 됩니다.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. 부모 자격 증명을 사용 하 여 Azure PowerShell에 로그인 하 고 다음 명령을 실행 하 여 역할 할당에 성공 했는지 확인 합니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 권한이 부모에 성공적으로 전파 되 고 세션에 추가 된 경우 원격 테 넌 트가 소유한 구독은 명령의 출력에 표시 됩니다.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>VNet을 허브에 연결

다음 단계에서는 가상 네트워크를 가상 허브에 연결할 때 두 구독의 컨텍스트 간을 전환 합니다. 사용자 환경에 맞게 예제 값을 바꿉니다.

1. 다음 명령을 실행 하 여 원격 계정의 컨텍스트에 있는지 확인 합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. 허브에 연결 하려는 가상 네트워크의 메타 데이터를 저장 하는 지역 변수를 만듭니다.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. 부모 계정으로 다시 전환 합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. VNet을 허브에 연결 합니다.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. PowerShell 또는 Azure Portal에서 새 연결을 볼 수 있습니다.

   * **PowerShell:** 연결을 성공적으로 구성 했으면 새로 만든 연결의 메타 데이터가 PowerShell 콘솔에 표시 됩니다.
   * **Azure Portal:** 가상 허브, **연결 > Virtual Network 연결** 로 이동 합니다. 연결에 대 한 포인터를 볼 수 있습니다. 실제 리소스를 보려면 적절 한 권한이 필요 합니다.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>문제 해결

* $Remote [의 메타](#connect)데이터가 Azure Portal의 정보와 일치 하는지 확인 합니다.
* 원격 테 넌 트 리소스 그룹의 IAM 설정이 나 Azure PowerShell 명령 (AzSubscription)을 사용 하 여 사용 권한을 확인할 수 있습니다.
* 리소스 그룹의 이름 또는 다른 환경 관련 변수 (예: "VirtualHub1" 또는 "VirtualNetwork1").

## <a name="next-steps"></a>다음 단계

가상 WAN에 대 한 자세한 내용은 다음을 참조 하세요.

* 가상 WAN [FAQ](virtual-wan-faq.md)
