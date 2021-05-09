---
title: Azure Load Balancer 배포 모드 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 원본 IP 선호도를 지원하도록 Azure Load Balancer의 배포 모드 구성을 시작합니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 44c56b91a02d53126f00ad64a1404ad4664c6a7f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886625"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Azure Load Balancer의 배포 모드 구성

Azure Load Balancer는 애플리케이션에 트래픽을 배포하는 두 가지 배포 모드를 지원합니다.

* 해시 기반
* 원본 IP 선호도

이 문서에서는 Azure Load Balancer의 배포 모드를 구성하는 방법을 알아봅니다.


## <a name="configure-distribution-mode"></a>배포 모드 구성

---

# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal)

포털에서 부하 분산 규칙을 수정하여 배포 모드의 구성을 변경할 수 있습니다.

1. Azure Portal에 로그인하고 **리소스 그룹** 을 클릭하여 변경하려는 부하 분산 장치를 포함하는 리소스 그룹을 찾습니다.
2. 부하 분산 장치 개요 화면의 **설정** 에서 **부하 분산 규칙** 을 선택합니다.
3. 부하 분산 규칙 화면에서 배포 모드를 변경하려는 부하 분산 규칙을 선택합니다.
4. 규칙에서 **세션 지속성** 드롭다운 상자를 변경하여 배포 모드를 변경합니다. 

다음 옵션을 사용할 수 있습니다. 

* **없음(해시 기반)** - 동일한 클라이언트의 후속 요청이 모든 가상 머신에서 처리되도록 지정합니다.
* **클라이언트 IP(원본 IP 선호도 2 튜플)** - 동일한 클라이언트 IP 주소의 후속 요청이 동일한 가상 머신에서 처리되도록 지정합니다.
* **클라이언트 IP 및 프로토콜(원본 IP 선호도 3 튜플)** - 동일한 클라이언트 IP 주소 및 프로토콜 조합의 후속 요청이 동일한 가상 머신에서 처리되도록 지정합니다.

5. 배포 모드를 선택한 다음, **저장** 을 선택합니다.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="부하 분산 장치 규칙에서 세션 지속성 변경" border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용하여 기존 부하 분산 규칙의 부하 분산 장치 배포 설정을 변경합니다. 다음은 배포 모드를 업데이트하는 명령입니다. 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

필요한 부하 분산의 유형에 대해 `LoadDistribution` 요소의 값을 설정합니다. 

* 2 튜플(원본 IP 및 대상 IP) 부하 분산에 대해 **SourceIP** 를 지정합니다. 

* 3 튜플(원본 IP, 대상 IP 및 프로토콜 유형) 부하 분산에 대해 **SourceIPProtocol** 을 지정합니다. 

* 5 튜플 부하 분산의 기본 동작에 대해 **Default** 를 지정합니다.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Azure CLI를 사용하여 기존 부하 분산 규칙의 부하 분산 장치 배포 설정을 변경합니다.  다음은 배포 모드를 업데이트하는 명령입니다.

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
필요한 부하 분산의 유형에 대해 `--load-distribution`의 값을 설정합니다.

* 2 튜플(원본 IP 및 대상 IP) 부하 분산에 대해 **SourceIP** 를 지정합니다. 

* 3 튜플(원본 IP, 대상 IP 및 프로토콜 유형) 부하 분산에 대해 **SourceIPProtocol** 을 지정합니다. 

* 5 튜플 부하 분산의 기본 동작에 대해 **Default** 를 지정합니다.

이 문서에서 사용되는 명령에 대한 자세한 내용은 [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update)를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

* [Azure Load Balancer개요](load-balancer-overview.md)
* [인터넷 연결 부하 분산 장치 구성 시작](quickstart-load-balancer-standard-public-powershell.md)
* [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
