---
title: "Azure Load Balancer에 대한 고가용성 포트 구성 | Microsoft Docs"
description: "모든 포트에서 내부 트래픽의 부하를 분산하는 고가용성 포트 사용 방법 알아보기"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 17fee798661b7db4f9933684fceefbfed51409cd
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>내부 부하 분산 장치에 대해 고가용성 포트를 구성하는 방법

이 문서에서는 내부 부하 분산 장치에 HA(고가용성) 포트를 배포하는 예제를 제공합니다. 네트워크 가상 어플라이언스 특정 구성의 경우 해당 공급자 웹 사이트를 참조하세요.

그림 1에서는 다음과 같이 이 문서에서 설명하는 배포 예제의 구성을 보여 줍니다.
- NVA는 HA 포트 구성 뒤에 있는 내부 부하 분산 장치의 백 엔드 풀에 배포됩니다. 
- DMZ 서브넷에 적용된 UDR은 다음 홉을 내부 부하 분산 장치 가상 IP로 만들어 <?>에 모든 트래픽을 라우트합니다. 
- 내부 부하 분산 장치는 LB 알고리즘에 따라 활성 NVA 중 하나에 트래픽을 분산합니다.
- NVA는 트래픽을 처리하여 백 엔드 서브넷의 원래 대상으로 전달합니다.
- 반환 경로는 해당 UDR이 백 엔드 서브넷에 구성되어 있는 경우에도 동일한 경로를 사용할 수 있습니다. 

![ha 포트 배포 예제](./media/load-balancer-configure-ha-ports/haports.png)

그림 1 - 고가용성 포트가 있는 내부 부하 분산 장치 뒤에 배포된 네트워크 가상 어플라이언스 

## <a name="configuring-ha-ports"></a>HA 포트 구성

HA 포트의 구성에는 백 엔드 풀에 NVA가 있는 내부 부하 분산 장치, NVA 상태를 검색하는 해당 부하 분산 장치 상태 프로브 구성 및 HA 포트가 있는 부하 분산 장치 규칙 설정이 포함됩니다. 일반적인 부하 분산 장치 관련 구성에 대해서는 [시작](load-balancer-get-started-ilb-arm-portal.md)을 참조하세요. 이 문서에서는 HA 포트 구성을 중점적으로 설명합니다.

기본적으로 이 구성에서는 프런트 엔드 포트와 백 엔드 포트 값을 **0**으로, 프로토콜 값을 **All**로 설정합니다. 이 문서에서는 Azure Portal, PowerShell 및 Azure CLI 2.0을 사용하여 고가용성 포트를 구성하는 방법에 대해 설명합니다.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Azure Portal을 사용하여 HA 포트 부하 분산 장치 규칙 구성

Azure Portal은 이 구성에서 **HA 포트** 옵션을 확인란으로 제공합니다. 이 옵션을 선택하면 관련 포트 및 프로토콜 구성이 자동으로 채워집니다. 

![Azure Portal을 통한 ha 포트 구성](./media/load-balancer-configure-ha-ports/haports-portal.png)

그림 2 - 포털을 통한 HA 포트 구성

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>PowerShell을 사용하여 HA 포트 부하 분산 장치 규칙 구성

다음 명령을 통해 PowerShell을 사용하여 내부 부하 분산 장치를 만드는 동안 HA 포트 부하 분산 장치 규칙을 만듭니다.

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Azure CLI 2.0을 사용하여 HA 포트 부하 분산 장치 규칙 구성

[내부 부하 분산 장치 설정 만들기](load-balancer-get-started-ilb-arm-cli.md)의 #4단계에서 다음 명령을 사용하여 HA 포트 부하 분산 장치 규칙을 만듭니다.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>다음 단계

- [고가용성 포트](load-balancer-ha-ports-overview.md)에 대해 자세히 알아보기

