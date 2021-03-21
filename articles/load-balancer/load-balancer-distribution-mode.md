---
title: Azure Load Balancer 배포 모드 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 원본 IP 선호도를 지원 하도록 Azure Load Balancer에 대 한 배포 모드를 구성 하기 시작 합니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2021
ms.author: allensu
ms.openlocfilehash: 2d8d5d84d32cdb8cc813d033f3f3fbb453b538fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739919"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Azure Load Balancer의 배포 모드 구성

Azure Load Balancer는 응용 프로그램에 트래픽을 분산 하기 위한 두 가지 배포 모드를 지원 합니다.

* 해시 기반
* 원본 IP 선호도

이 문서에서는 Azure Load Balancer에 대 한 배포 모드를 구성 하는 방법에 대해 알아봅니다.


## <a name="configure-distribution-mode"></a>배포 모드 구성

---

# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal)

포털에서 부하 분산 규칙을 수정 하 여 배포 모드의 구성을 변경할 수 있습니다.

1. Azure Portal에 로그인 하 고 **리소스 그룹** 을 클릭 하 여 변경 하려는 부하 분산 장치를 포함 하는 리소스 그룹을 찾습니다.
2. 부하 분산 장치 개요 화면의 **설정** 에서 **부하 분산 규칙** 을 선택 합니다.
3. 부하 분산 규칙 화면에서 배포 모드를 변경 하려는 부하 분산 규칙을 선택 합니다.
4. 규칙 아래에서 **세션 지 속성** 드롭다운 상자를 변경 하 여 배포 모드를 변경 합니다. 

다음 옵션을 사용할 수 있습니다. 

* **없음 (해시 기반)** -동일한 클라이언트의 후속 요청이 가상 머신에서 처리 될 수 있도록 지정 합니다.
* **클라이언트 ip (원본 ip 선호도 2 튜플)** -동일한 클라이언트 ip 주소의 연속 요청을 동일한 가상 컴퓨터에서 처리 하도록 지정 합니다.
* **클라이언트 ip 및 프로토콜 (원본 IP 선호도 3 튜플)** -동일한 클라이언트 ip 주소 및 프로토콜 조합의 연속 요청을 동일한 가상 컴퓨터에서 처리 하도록 지정 합니다.

5. 배포 모드를 선택 하 고 **저장** 을 선택 합니다.

:::image type="content" source="./media/load-balancer-distribution-mode/session-persistence.png" alt-text="부하 분산 장치 규칙에 대 한 세션 지 속성을 변경 합니다." border="true":::


# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용 하 여 기존 부하 분산 규칙에 대 한 부하 분산 장치 배포 설정을 변경 합니다. 다음 명령은 배포 모드를 업데이트 합니다. 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLoadBalancer -ResourceGroupName MyResourceGroupLB
$lb.LoadBalancingRules[0].LoadDistribution = 'default'
Set-AzLoadBalancer -LoadBalancer $lb
```

`LoadDistribution`필요한 부하 분산 유형에 대 한 요소의 값을 설정 합니다. 

* 두 튜플 (원본 IP 및 대상 IP) 부하 분산에 대해 **SourceIP** 를 지정 합니다. 

* 3 튜플 (원본 IP, 대상 IP 및 프로토콜 유형) 부하 분산에 대해 **Sourceipprotocol** 을 지정 합니다. 

* 5 튜플 부하 분산의 기본 동작에 대 한 **기본값** 을 지정 합니다.

# <a name="cli"></a>[**CLI**](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

기존 부하 분산 규칙에 대 한 부하 분산 장치 배포 설정을 변경 하려면 Azure CLI을 사용 합니다.  다음 명령은 배포 모드를 업데이트 합니다.

```azurecli-interactive
az network lb rule update \
    --lb-name myLoadBalancer \
    --load-distribution Default \
    --name myHTTPRule \
    --resource-group myResourceGroupLB 
```
`--load-distribution`필요한 부하 분산 유형에 값을 설정 합니다.

* 두 튜플 (원본 IP 및 대상 IP) 부하 분산에 대해 **SourceIP** 를 지정 합니다. 

* 3 튜플 (원본 IP, 대상 IP 및 프로토콜 유형) 부하 분산에 대해 **Sourceipprotocol** 을 지정 합니다. 

* 5 튜플 부하 분산의 기본 동작에 대 한 **기본값** 을 지정 합니다.

이 문서에서 사용 되는 명령에 대 한 자세한 내용은 [az network lb rule update](/cli/azure/network/lb/rule#az_network_lb_rule_update) 를 참조 하세요.

---

## <a name="next-steps"></a>다음 단계

* [Azure Load Balancer개요](load-balancer-overview.md)
* [인터넷 연결 부하 분산 장치 구성 시작](quickstart-load-balancer-standard-public-powershell.md)
* [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)