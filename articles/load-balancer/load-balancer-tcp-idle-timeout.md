---
title: Azure에서 부하 분산 장치 TCP 유휴 시간 제한 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure Load Balancer TCP 유휴 시간 제한을 구성 하는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 39cd5b5d6e9d6007994ccc29732186ec6a8bdc2e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284136"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Azure Load Balancer에 대한 TCP 유휴 시간 제한 설정 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="tcp-idle-timeout"></a>TCP 유휴 시간 제한
기본 구성에서 Azure 부하 분산 장치의 '유휴 시간 제한' 설정은 4분입니다. 비활성 기간이 시간 제한 값보다 긴 경우 클라이언트와 클라우드 서비스 간의 TCP 또는 HTTP 세션이 유지되지 않을 수 있습니다.

연결이 닫히면 클라이언트 애플리케이션에 "기본 연결이 닫혔습니다. 활성 상태로 유지될 것으로 예상된 연결이 서버에서 닫혔습니다."와 같은 오류 메시지가 표시될 수 있습니다.

일반적인 방법은 TCP 연결 유지를 사용하는 것입니다. 이 방법은 더 오랜 기간 동안 연결을 활성 상태로 유지합니다. 자세한 내용은 이러한 [.NET 예제](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)를 참조하세요. 연결 유지를 사용하면 연결 비활성화 기간 동안 패킷이 전송됩니다. Keep-alive 패킷 유휴 시간 제한 값에 도달 하지 않고 오랜 시간 동안 연결이 유지 되도록 합니다.

설정은 인바운드 연결에 대해서만 작동 합니다. 연결이 끊어지지 않도록 하려면 유휴 시간 제한 설정 보다 작은 간격으로 TCP 연결 유지를 구성 하거나 유휴 시간 제한 값을 늘립니다. 이러한 시나리오를 지원 하기 위해 구성 가능한 유휴 시간 제한에 대 한 지원이 추가 되었습니다. 이제 4분에서 30분 사이의 기간으로 설정할 수 있습니다.

TCP 연결 유지는 배터리 수명이 제약 조건이 아닌 시나리오에 대해 작동 합니다. 모바일 응용 프로그램에는 권장 되지 않습니다. 모바일 애플리케이션에서 TCP 연결 유지를 사용하면 디바이스 배터리가 더 빨리 방전될 수 있습니다.

![TCP 시간 제한](./media/load-balancer-tcp-idle-timeout/image1.png)

다음 섹션에서는 공용 IP 및 부하 분산 장치 리소스에 대 한 유휴 시간 제한 설정을 변경 하는 방법을 설명 합니다.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>인스턴스 수준 공용 IP의 TCP 시간 제한을 15분으로 구성

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes`는 선택 사항입니다. 설정하지 않으면 기본 시간 제한은 4분입니다. 허용되는 시간 제한 범위는 4분에서 30분 사이입니다.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>부하 분산 된 규칙에 대 한 TCP 제한 시간을 15 분으로 설정 합니다.

부하 분산 장치에 대 한 유휴 시간 제한을 설정 하기 위해 부하 분산 규칙에 ' IdleTimeoutInMinutes '가 설정 됩니다. 다음은 그 예입니다.

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 개요](load-balancer-internal-overview.md)

[인터넷 연결 부하 분산 장치 구성 시작](quickstart-create-standard-load-balancer-powershell.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)
