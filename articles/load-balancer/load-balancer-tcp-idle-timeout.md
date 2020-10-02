---
title: Azure에서 부하 분산 장치 TCP 다시 설정 및 유휴 시간 제한 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure Load Balancer TCP 유휴 시간 제한을 구성하는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649850"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer에 대 한 TCP 유휴 시간 제한 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

Azure Load Balancer의 유휴 시간 제한 설정은 4 분 120 분입니다. 기본적으로 4분으로 설정되어 있습니다. 비활성 기간이 시간 제한 값보다 긴 경우 클라이언트와 클라우드 서비스 간의 TCP 또는 HTTP 세션이 유지되지 않을 수 있습니다. [TCP 유휴 시간 제한](load-balancer-tcp-reset.md)에 대해 자세히 알아보세요.

다음 섹션에서는 공용 IP 및 부하 분산 장치 리소스의 유휴 시간 제한 설정을 변경하는 방법을 설명합니다.


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>공용 IP에 대 한 TCP 유휴 시간 제한 구성

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes`는 선택 사항입니다. 설정하지 않으면 기본 시간 제한은 4분입니다. 허용 되는 시간 제한 범위는 4 ~ 120 분입니다.

## <a name="set-the-tcp-idle-timeout-on-rules"></a>규칙에 대 한 TCP 유휴 시간 제한 설정

부하 분산 장치의 유휴 시간 제한을 설정하기 위해 부하 분산 규칙에 'IdleTimeoutInMinutes'가 설정되어 있습니다. 다음은 그 예입니다.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 개요](load-balancer-internal-overview.md)

[인터넷 연결 부하 분산 장치 구성 시작](quickstart-load-balancer-standard-public-powershell.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)
