---
title: 'Azure 상태 모니터 v2 API 참조: 구성을 가져올 | Microsoft Docs'
description: 상태 모니터 v2 API 참조입니다. Get-ApplicationInsightsMonitoringConfig. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스 또는 Azure Vm에서 호스트 하는 ASP.NET 웹 앱에서 작동 합니다.
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
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514393"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>상태 모니터 v2 API: Get-ApplicationInsightsMonitoringConfig (v0.2.1 알파)

이 문서에서는 설명의 구성원임을 확인 하는 cmdlet를 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 하지 않습니다. 일부 기능은 지원 되지 않는, 및 일부 기능이 제한 될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="description"></a>설명

구성 파일을 가져오고 콘솔에 인쇄 합니다.

> [!IMPORTANT] 
> 이 cmdlet은 관리자 권한으로 PowerShell 세션에 필요합니다.

## <a name="examples"></a>예

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>매개 변수

필요한 매개 변수 없이 합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-reading-the-config-file"></a>구성 파일을 읽는 예제 출력

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md) 성능 모니터링을 사용 합니다.
- [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제를 진단 합니다.
- 사용 하 여 [analytics](../../azure-monitor/app/analytics.md) 자세한 고급 쿼리 합니다.
- [대시보드를 만들](../../azure-monitor/app/overview-dashboard.md)합니다.
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
- [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록할 수 있도록 합니다.
 
 상태 모니터 v2 사용 하 여 더 수행 합니다.
 - 가이드를 사용 하 여 [해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
 - 사용 하 여 구성에 변경 된 [구성을 설정할](status-monitor-v2-api-set-config.md) cmdlet.
