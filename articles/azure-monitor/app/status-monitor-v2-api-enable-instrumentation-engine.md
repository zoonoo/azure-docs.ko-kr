---
title: 'Azure 상태 모니터 v2 API 참조: 계측 엔진 사용 | Microsoft Docs'
description: V2 API 참조를 상태 모니터 합니다. InstrumentationEngine를 사용 합니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
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
ms.openlocfilehash: 3c0f4e015d3e01e86daaf101c15e16857540a520
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033161"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine"></a>상태 모니터 v2 API: InstrumentationEngine

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>Description

일부 레지스트리 키를 설정 하 여 계측 엔진을 사용 하도록 설정 합니다.
변경 내용을 적용 하려면 IIS를 다시 시작 하십시오.

계측 엔진은 .NET Sdk에 의해 수집 된 데이터를 보완할 수 있습니다.
관리 되는 프로세스의 실행을 설명 하는 이벤트 및 메시지를 수집 합니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 [SQL 명령 텍스트가](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)포함 됩니다.

다음의 경우 계측 엔진을 사용 하도록 설정 합니다.
- Enable cmdlet을 사용 하 여 모니터링을 이미 사용 하도록 설정 했지만 계측 엔진을 사용 하도록 설정 하지 않았습니다.
- 수동으로 .NET Sdk를 사용 하 여 앱을 계측 하 고 추가 원격 분석을 수집 하려고 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.

> [!NOTE] 
> - 이 cmdlet을 사용 하려면 라이선스 및 개인 정보 취급 방침을 검토 하 고 동의 해야 합니다.
> - 계측 엔진은 추가 오버 헤드를 추가 하며 기본적으로 해제 되어 있습니다.

## <a name="examples"></a>예

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>매개 변수

### <a name="-acceptlicense"></a>-AcceptLicense
**선택** 이 스위치를 사용 하 여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침에 동의 합니다.

### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 출력 합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>계측 엔진을 사용 하도록 설정 하는 예제 출력

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭을 탐색](../../azure-monitor/app/metrics-explorer.md) 하 여 성능 및 사용량을 모니터링 합니다.
- [이벤트와 로그를 검색](../../azure-monitor/app/diagnostic-search.md) 하 여 문제를 진단 합니다.
- 고급 쿼리를 위해 [분석](../../azure-monitor/app/analytics.md) 을 사용 합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되는지 확인 합니다.
- 웹 [클라이언트 원격 분석을 추가](../../azure-monitor/app/javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](../../azure-monitor/app/asp-net.md) 합니다.
 
 상태 모니터 v2를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 상태 모니터 v2 [문제를 해결](status-monitor-v2-troubleshoot.md) 하세요.
 - [구성을 가져와서](status-monitor-v2-api-get-config.md) 설정이 올바르게 기록 되었는지 확인 합니다.
 - 모니터링을 검사할 [상태를 가져옵니다](status-monitor-v2-api-get-status.md) .
