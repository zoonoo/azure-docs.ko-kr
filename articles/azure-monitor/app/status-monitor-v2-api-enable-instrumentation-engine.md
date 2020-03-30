---
title: Azure 응용 프로그램 인사이트 에이전트 API 참조
description: 응용 프로그램 인사이트 에이전트 API 참조. 인에이블레이션 엔진. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671360"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>애플리케이션 인사이트 에이전트 API: 인에이블-계측엔진

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

## <a name="description"></a>설명

일부 레지스트리 키를 설정하여 계측 엔진을 활성화합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

계측 엔진은 .NET SDK에서 수집한 데이터를 보완할 수 있습니다.
관리되는 프로세스의 실행을 설명하는 이벤트 및 메시지를 수집합니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 [SQL 명령 텍스트가](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)포함됩니다.

다음과 같은 경우 계측 엔진을 활성화합니다.
- 인에이블 cmdlet을 사용하여 모니터링을 이미 활성화했지만 계측 엔진을 사용하도록 설정하지 않았습니다.
- .NET SDK를 사용하여 앱을 수동으로 계측했으며 추가 원격 분석을 수집하려고 합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션이 필요합니다.

> [!NOTE] 
> - 이 cmdlet은 당사의 라이선스 및 개인 정보 취급 방침을 검토하고 수락하도록 요구합니다.
> - 계측 엔진은 추가 오버헤드를 추가하고 기본적으로 꺼져 있습니다.

## <a name="examples"></a>예

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>매개 변수

### <a name="-acceptlicense"></a>- 수락라이센스
**선택적.** 이 스위치를 사용하여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침을 수락합니다.

### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 출력합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>계측 엔진을 성공적으로 활성화한 결과물 예제

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭을 탐색하여](../../azure-monitor/app/metrics-explorer.md) 성능 및 사용량을 모니터링합니다.
- [이벤트 및 로그를 검색하여](../../azure-monitor/app/diagnostic-search.md) 문제를 진단합니다.
- 고급 쿼리에 [대한 분석을](../../azure-monitor/app/analytics.md) 사용합니다.
- [대시보드를 만듭니다.](../../azure-monitor/app/overview-dashboard.md)
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가하여](../../azure-monitor/app/javascript.md) 웹 페이지 코드의 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [코드에 응용 프로그램 인사이트 SDK를 추가합니다.](../../azure-monitor/app/asp-net.md)
 
 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
 - 설정이 올바르게 기록되었는지 확인하기 위해 [구성을 가져옵니다.](status-monitor-v2-api-get-config.md)
 - 모니터링을 검사할 [상태를 가져옵니다.](status-monitor-v2-api-get-status.md)
