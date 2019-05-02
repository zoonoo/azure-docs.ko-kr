---
title: Azure 상태 모니터 v2 개요 | Microsoft Docs
description: 상태 모니터 v2 간략히 설명 합니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: fac14281365ccf3c191684af8cfdebda69e734e0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870449"
---
# <a name="status-monitor-v2"></a>상태 모니터 v2

상태 모니터 v2는 PowerShell 모듈에 게시 합니다 [PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) 대체 되며 [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)합니다. 이 모듈에서는 IIS를 사용 하 여 호스팅된.NET 웹 응용 프로그램의 코드를 사용 하지 않는 계측을 제공 합니다.
원격 분석 전송 됩니다 Azure portal로 할 수 있습니다 [모니터](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 응용 프로그램입니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>PowerShell 갤러리

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>지침
- 검토 우리의 [지침 시작](status-monitor-v2-get-started.md) 간결한 코드 샘플을 사용 하 여 지금 시작 하 합니다.
- 검토 우리의 [상세 지침](status-monitor-v2-detailed-instructions.md) 시작 하는 방법에 자세히입니다.

## <a name="powershell-api-reference"></a>PowerShell API 참조
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>문제 해결
- [문제 해결](status-monitor-v2-troubleshoot.md)
- [알려진 문제](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- 상태 모니터 v2 프록시 설치를 지원 하나요?

  **예**합니다. 상태 모니터 v2를 다운로드 하는 여러 옵션이 있습니다. 컴퓨터에 인터넷 액세스를 등록할 수 있습니다 사용 하 여 PowerShell 갤러리 `-Proxy` 매개 변수입니다. 이 모듈을 수동으로 다운로드할 수 있으며 컴퓨터에 설치 하거나 또는 직접 모듈을 사용 합니다. 이러한 각 옵션에 설명 되어 우리의 [지침은](status-monitor-v2-detailed-instructions.md)합니다.
  
- 사용을 확인 하는 방법을 성공적으로?

   해당 사용 성공 했는지 확인 하려면 cmdlet이 없었습니다. 사용 하는 것이 좋습니다 [라이브 메트릭](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) 신속 하 게 하는 경우 응용 프로그램은 보내는 원격 분석을 확인할 수 있습니다.
