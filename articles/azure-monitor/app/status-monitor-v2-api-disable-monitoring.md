---
title: 'Azure 애플리케이션 Insights 에이전트 API 참조: 모니터링 사용 안 함 | Microsoft Docs'
description: 에이전트 API 참조를 Application Insights 합니다. ApplicationInsightsMonitoring를 사용 하지 않습니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d305e664c68af34027510a4e27d293bb24abc021
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389909"
---
# <a name="application-insights-agent-api-disable-applicationinsightsmonitoring"></a>Application Insights 에이전트 API: ApplicationInsightsMonitoring

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>설명

대상 컴퓨터에서 모니터링을 사용 하지 않도록 설정 합니다.
이 cmdlet은 IIS Applicationhost.config의 편집 내용을 제거 하 고 레지스트리 키를 제거 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.

## <a name="examples"></a>예시

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>parameters 

### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 표시 합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-successfully-disabling-monitoring"></a>모니터링을 사용 하지 않도록 설정 하는 예제 출력

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="next-steps"></a>다음 단계

 Application Insights 에이전트를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 Application Insights 에이전트 [문제를 해결할](status-monitor-v2-troubleshoot.md) 수 있습니다.
