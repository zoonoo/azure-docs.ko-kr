---
title: Azure 응용 프로그램 인사이트 에이전트 비활성화 계측엔진
description: 응용 프로그램 인사이트 에이전트 API 참조. 계측 해제엔진. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998378"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>애플리케이션 인사이트 에이전트 API: 인스트루먼트 엔진 사용 안 함

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

## <a name="description"></a>설명
일부 레지스트리 키를 제거하여 계측 엔진을 사용하지 않도록 설정합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션이 필요합니다.

## <a name="examples"></a>예

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>매개 변수 

### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 출력합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>계측 엔진을 성공적으로 비활성화한 결과

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>다음 단계

 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
