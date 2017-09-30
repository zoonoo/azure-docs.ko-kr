---
title: "Azure Portal을 사용하여 Azure DDoS 보호 표준 관리 | Microsoft Docs"
description: "Azure Monitor에서 Azure DDoS 보호 표준 원격 분석을 사용하여 공격을 완화하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2b55341a9d5107376557147913f4fb6cb3966bae
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure DDoS 보호 표준 관리

>[!IMPORTANT]
>Azure DDoS 보호 표준(DDoS 보호)은 현재 미리 보기로 제공됩니다. 제한된 수의 Azure 리소스가 선택한 수의 지역에서 DDoS 보호를 지원합니다. 제한된 미리 보기 동안 구독에 대해 DDoS 보호를 사용하도록 설정하려면 [서비스에 등록](http://aka.ms/ddosprotection)해야 합니다. 등록 시 Azure DDoS 팀에서 사용 설정 프로세스를 안내하기 위해 연락합니다. DDoS 보호는 미국 동부, 미국 서부 및 미국 중부 지역에서 사용할 수 있습니다. 미리 보기 중에는 이 서비스 사용 요금이 청구되지 않습니다.

이 문서에서는 Azure Portal을 사용하여 DDoS 보호를 사용하거나 사용하지 않도록 설정하는 방법 및 원격 분석을 사용하는 방법을 보여 줍니다. 

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="enable-ddos-protection"></a>DDoS 보호 사용

신규 또는 기존 가상 네트워크에서 DDoS 보호를 사용합니다.

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>새 가상 네트워크를 만들고 DDoS 보호를 사용하도록 설정

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.
2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.
3. 가상 네트워크 정보를 입력합니다. *DDoS 보호*에서 **사용**을 클릭한 후 **만들기**를 클릭합니다.

    ![가상 네트워크 만들기](./media/ddos-protection-manage-portal/ddos-create-vnet.png)   

DDoS 보호에서 요금을 부과할 수 있음을 알리는 경고입니다. 미리 보기 중에는 DDoS 보호에 대한 요금이 부과되지 않습니다. 일반 공급(GA) 시 요금이 부과되고 고객은 부과 시작 및 GA 30일 전에 알림을 받습니다.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>기존 가상 네트워크에서 DDoS 보호 사용 

1. Azure Portal 메뉴에서 **가상 네트워크**를 클릭한 후 가상 네트워크를 선택합니다.
2. **DDoS 보호**를 클릭하고 *DDoS 보호* 화면에서 **사용**을 클릭한 후 **저장**을 클릭합니다. 

DDoS 보호에서 요금을 부과할 수 있음을 알리는 경고입니다. 미리 보기 중에는 DDoS 보호에 대한 요금이 부과되지 않습니다. 일반 공급(GA) 시 요금이 부과되고 고객은 부과 시작 및 GA 30일 전에 알림을 받습니다.

## <a name="disable-ddos-protection"></a>DDoS 보호 사용 안 함

1. Azure Portal 메뉴에서 **가상 네트워크**를 클릭한 후 가상 네트워크를 선택합니다.
2. **DDoS 보호**를 클릭하고 *DDoS 보호* 화면에서 **사용 안 함**을 클릭한 후 **저장**을 클릭합니다.

## <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 경고 구성

Azure Monitor 경고 구성을 통해 사용 가능한 DDoS 보호 메트릭을 선택하여 공격 중에 위험이 실제로 완화될 경우 알릴 수 있습니다. 조건에 부합하면 지정된 주소로 전자 메일 경고가 수신됩니다.

1. **모니터**를 클릭한 후 **메트릭**을 클릭합니다.
2. *메트릭* 화면에서 리소스 그룹, **공용 IP 주소**의 리소스 유형, Azure 공용 IP를 선택하세요.
3. 메트릭에 대한 전자 메일 경고를 구성하려면 **경고를 추가하려면 클릭**을 클릭합니다. 모든 메트릭에 대한 전자 메일 경고를 만들 수 있지만 가장 확실한 메트릭은 **DDoS 공격 상태이거나 그렇지 않은 상태**입니다. 부울 값 1 또는 0입니다. **1**은 공격을 받고 있는 것입니다. **0**은 공격을 받고 있지 않은 것입니다.
4. 공격을 받고 있을 때 전자 메일을 받으려면 **DDoS 공격 상태이거나 그렇지 않은 상태** 및 **지난 5분 동안 0보다 큰 조건**에 대한 메트릭을 설정합니다. 다른 메트릭에도 비슷한 경고를 설정할 수 있습니다.

    ![메트릭 구성](./media/ddos-protection-manage-portal/ddos-metrics.png)

    공격이 감지되면 몇 분 안에 Azure Monitor 메트릭을 통해 알림이 전송됩니다.

    ![공격 경고](./media/ddos-protection-manage-portal/ddos-alert.png) 

경고를 만들기 위해 [웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md) 및 [Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md)에 대해 자세히 알아볼 수도 있습니다.

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS 보호 메트릭에 대한 로깅 구성

1. **모니터**를 클릭한 후 **진단 설정**을 클릭합니다.
2. *메트릭* 화면에서 리소스 그룹, **공용 IP 주소**의 리소스 유형, Azure 공용 IP를 선택하세요.
3. **다음 데이터를 수집하려면 진단 사용**을 클릭합니다.

로깅에 사용할 수 있는 세 가지 옵션이 있습니다.

- **저장소 계정에 보관** - 저장소 계정에 로그를 기록합니다.
- **이벤트 허브로의 스트림** – 로그 수신기가 이벤트 허브를 사용하여 로그를 받을 수 있도록 허용합니다. 그러면 Splunk 또는 기타 SIEM 시스템과 통합할 수 있습니다.
- **Log Analytics에 보내기** – Azure OMS Log Analytics 서비스에 로그를 기록합니다.

## <a name="use-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 사용

공격에 대한 원격 분석이 Azure Monitor를 통해 실시간으로 제공됩니다. 이러한 원격 분석 기능은 공용 IP 주소의 위험이 완화되는 동안에만 사용할 수 있습니다. 공격이 완화되기 이전 또는 이후에는 원격 분석이 표시되지 않습니다.

1. **모니터**를 클릭한 후 **메트릭**을 클릭합니다. 
2. *메트릭* 화면에서 리소스 그룹, **공용 IP 주소**의 리소스 유형, Azure 공용 IP를 선택하세요. 일련의 사용 가능한 메트릭이 화면 왼쪽에 나타납니다. 이러한 메트릭을 선택할 경우 개요 화면의 Azure Monitor 메트릭 차트에 그래프로 표시됩니다. 

메트릭 이름은 다양한 패킷 유형과 바이트 및 패킷을 나타내며, 각 메트릭에서 태그 이름의 기본 구조는 다음과 같습니다.

- **Dropped 태그 이름(예: Inbound Packets Dropped DDoS):** DDoS 보호 시스템에서 삭제/스크럽한 패킷의 수입니다.
- **Forwarded 태그 이름(예: Inbound Packets Forwarded DDoS):** DDoS 시스템에서 대상 VIP로 전달한 패킷의 수입니다(필터링되지 않은 트래픽).
- **태그 이름 없음(예: Inbound Packets DDoS):** 스크럽 시스템으로 들어온 패킷의 총 수입니다(삭제 및 전달된 패킷의 합계를 나타냄).

## <a name="next-steps"></a>다음 단계

- [Azure 진단 로그에 대해 자세히 알아보기](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Azure Storage에서 Log Analytics를 사용하여 로그 분석](../log-analytics/log-analytics-azure-storage.md)
- [이벤트 허브 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
