---
title: Azure 애플리케이션 Insights 에이전트 API 참조
description: 에이전트 API 참조를 Application Insights 합니다. InstrumentationEngine를 사용 하지 않습니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: dafa12db95a10df467bf0d042cfd9395720648b2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671378"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights 에이전트 API: InstrumentationEngine

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>Description
일부 레지스트리 키를 제거 하 여 계측 엔진을 사용 하지 않도록 설정 합니다.
변경 내용을 적용 하려면 IIS를 다시 시작 하십시오.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.

## <a name="examples"></a>예

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>매개 변수 

### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 출력 합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>계측 엔진을 사용 하지 않도록 설정 하는 예제 출력

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>다음 단계

 Application Insights 에이전트를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 Application Insights 에이전트 [문제를 해결할](status-monitor-v2-troubleshoot.md) 수 있습니다.
