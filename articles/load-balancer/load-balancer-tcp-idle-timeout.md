---
title: Azure에서 로드 밸로저 TCP 유휴 시간 시간 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure 로드 밸러서 TCP 유휴 시간 시간을 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: d0bb73b58aa23e5f7eb784772acf37b05df463ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456831"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Azure Load Balancer에 대한 TCP 유휴 시간 제한 설정 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="tcp-idle-timeout"></a>TCP 유휴 시간
Azure 로드 밸런서에는 4분에서 30분까지의 유휴 시간 설정이 있습니다. 기본적으로 4분으로 설정됩니다. 비활성 기간이 시간 제한 값보다 긴 경우 클라이언트와 클라우드 서비스 간의 TCP 또는 HTTP 세션이 유지되지 않을 수 있습니다.

연결이 닫히면 클라이언트 애플리케이션에 "기본 연결이 닫혔습니다. 활성 상태로 유지될 것으로 예상된 연결이 서버에서 닫혔습니다."와 같은 오류 메시지가 표시될 수 있습니다.

일반적인 방법은 TCP 연결 유지를 사용하는 것입니다. 이 방법은 더 오랜 기간 동안 연결을 활성 상태로 유지합니다. 자세한 내용은 이러한 [.NET 예제](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)를 참조하세요. 연결 유지를 사용하면 연결 비활성화 기간 동안 패킷이 전송됩니다. Keep-alive 패킷은 유휴 시간 시간 값에 도달하지 않고 연결이 장기간 유지되도록 합니다.

이 설정은 인바운드 연결에만 작동합니다. 연결이 끊어지지 않도록 하려면 유휴 시간 초과 설정보다 적은 간격으로 TCP keep-alive를 구성하거나 유휴 시간 초과 값을 늘립니다. 이러한 시나리오를 지원하기 위해 구성 가능한 유휴 시간 시간에 대한 지원이 추가되었습니다.

TCP keep-alive는 배터리 수명이 제약이 아닌 시나리오에서 작동합니다. 모바일 응용 프로그램에는 권장되지 않습니다. 모바일 애플리케이션에서 TCP 연결 유지를 사용하면 디바이스 배터리가 더 빨리 방전될 수 있습니다.

![TCP 시간 제한](./media/load-balancer-tcp-idle-timeout/image1.png)

다음 섹션에서는 공용 IP 및 로드 밸런서 리소스에 대한 유휴 시간 설정 설정을 변경하는 방법을 설명합니다.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>인스턴스 수준 공용 IP의 TCP 시간 제한을 15분으로 구성

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes`는 선택 사항입니다. 설정하지 않으면 기본 시간 제한은 4분입니다. 허용되는 시간 제한 범위는 4분에서 30분 사이입니다.

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>부하 균형 규칙의 TCP 시간 초과를 15분으로 설정합니다.

로드 밸런서의 유휴 시간 초과를 설정하려면 로드 균형 규칙에 'IdleTimeoutInMinutes'이 설정됩니다. 예를 들어:

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 개요](load-balancer-internal-overview.md)

[인터넷 연결 부하 분산 장치 구성 시작](quickstart-create-standard-load-balancer-powershell.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)
