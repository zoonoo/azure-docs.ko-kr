---
title: Azure Monitor에서 다른 모니터링 서비스의 경고 관리
description: Azure Monitor에서 Nagios, Zabbix 및 SCOM 경고 관리
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: bc597d42fe89c0e03c4af1db3a935031b9043a98
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53345480"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>다른 모니터링 서비스의 경고 관리

이제 [통합 경고 환경](https://aka.ms/azure-alerts-overview)에서 Nagios, Zabbix 및 System Center Operations Manager의 경고를 볼 수 있습니다. 이러한 경고는 Nagios/Zabbix 서버 또는 System Center Operations Manager와의 통합에서 Log Analytics로 제공됩니다. 

## <a name="prerequisites"></a>필수 조건
Log Analytics 리포지토리에서 경고 유형인 모든 레코드를 통합 경고 환경으로 가져오므로, 이러한 레코드를 수집하는 데 필요한 구성을 수행해야 합니다.
1. **Nagios** 및 **Zabbix** 경고의 경우 Log Analytics로 경고를 보내도록 [해당 서버를 구성](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)합니다.
1. **System Center Operations Manager** 경고의 경우 [Operations Manager 관리 그룹을 Log Analytics 작업 영역에 연결](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)합니다. 그러면 System Center Operations Manager에서 생성된 모든 경고를 Log Analytics로 가져옵니다.

## <a name="view-your-alert-instances"></a>경고 인스턴스 보기
Log Analytics로 가져오기를 구성하고 나면, [통합 경고 환경](https://aka.ms/azure-alerts-overview)에서 이러한 모니터링 서비스의 경고 인스턴스 보기를 시작할 수 있습니다. 통합 경고 환경에 표시되면 [경고 인스턴스 관리](https://aka.ms/managing-alert-instances), [이 경고에 대해 생성된 스마트 그룹 관리](https://aka.ms/managing-smart-groups), [경고 및 스마트 그룹의 상태 변경](https://aka.ms/managing-alert-smart-group-states)을 수행할 수 있습니다.

> [!NOTE]
>  통합 경고 환경의 Nagios 경고는 상태 저장이 아닙니다. 예를 들어 경고의 [모니터 조건](https://aka.ms/azure-alerts-overview)이 “발생함”에서 “해결됨”으로 진행되지 않습니다. 대신, “발생함” 및 “해결됨”이 모두 별도의 경고 인스턴스로 표시됩니다. 
