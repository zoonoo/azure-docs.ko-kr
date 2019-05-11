---
title: 'Azure 상태 모니터 v2 API 참조: 모니터링 사용 안 함 | Microsoft Docs'
description: 상태 모니터 v2-ApplicationInsightsMonitoring API 참조 사용 하지 않도록 설정 합니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
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
ms.openlocfilehash: 6f2bde824c2a9815dc9fd4791ab43e03558da835
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144942"
---
# <a name="status-monitor-v2-api-disable-applicationinsightsmonitoring-v021-alpha"></a>상태 모니터 v2 API: 사용 안 함-ApplicationInsightsMonitoring (v0.2.1 알파)

이 문서에서는의 구성원으로 제공 되는 cmdlet을 설명 합니다 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>설명

대상 컴퓨터에서 모니터링 하는 사용 하지 않도록 설정 합니다.
이 cmdlet은 IIS applicationHost.config에 편집을 제거 하 고 레지스트리 키가 제거 됩니다.

> [!IMPORTANT] 
> 이 cmdlet은 관리자 권한으로 PowerShell 세션에 필요합니다.

## <a name="examples"></a>예

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>매개 변수 

### <a name="-verbose"></a>-Verbose
**일반적인 매개 변수입니다.** 자세한 로그를 출력 하려면이 스위치를 사용 합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-successfully-disabling-monitoring"></a>예제 출력에서 모니터링을 비활성화 했습니다.

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

 상태 모니터 v2 사용 하 여 더 수행 합니다.
 - 가이드를 사용 하 여 [문제 해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
