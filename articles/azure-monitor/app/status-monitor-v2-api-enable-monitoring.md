---
title: 'Azure 상태 모니터 v2 API 참조: 모니터링을 사용 하도록 설정 | Microsoft Docs'
description: 상태 모니터 v2-ApplicationInsightsMonitoring API 참조 사용 하도록 설정 합니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
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
ms.openlocfilehash: e2d964316e83138711547fb18fc5d04c56b4002f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870524"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>상태 모니터 v2 API: 사용-ApplicationInsightsMonitoring (v0.2.1 알파)

이 문서에서는의 구성원으로 제공 되는 cmdlet을 설명 합니다 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>설명

활성화 코드를 사용 하지 않는 대상 컴퓨터에서 IIS 응용 프로그램의 모니터링을 연결 합니다.
이 cmdlet은 IIS applicationHost.config를 수정 하 고 몇 가지 레지스트리 키 설정 됩니다.
이 cmdlet는 계측 키를 사용 하는 응용 프로그램에서 정의 하는 applicationinsights.ikey.config도 만듭니다.
IIS는 이러한 응용 프로그램 시작 Application Insights SDK를 응용 프로그램에 삽입 하는 시작 시 RedfieldModule 로드 됩니다.
변경 내용을 적용 하려면 IIS를 다시 시작 합니다.

모니터링을 사용 하도록 설정한 후 사용 하 여 권장 [라이브 메트릭](live-stream.md) 신속 하 게 하는 경우 응용 프로그램은 보내는 원격 분석을 확인할 수 있습니다.


> [!NOTE] 
> 시작 하려면 계측 키가 있어야 합니다. 자세한 내용은 [새 리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)합니다.


> [!IMPORTANT] 
> 이 cmdlet에는 상승 된 실행 정책 및 관리자 권한으로 PowerShell 세션에 필요합니다. 읽기 [여기](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) 자세한 내용은 합니다.

> [!NOTE] 
> 이 cmdlet를 검토 하 고이 라이선스 및 개인정보취급방침 적용 해야 합니다.


## <a name="examples"></a>예

### <a name="example-with-single-instrumentation-key"></a>단일 계측 키를 사용 하 여 예제
이 예제에서는 현재 컴퓨터의 모든 응용 프로그램 단일 계측 키를 할당 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>계측 키 맵 사용 하 여 예제
이 예제에서는 다음이 적용됩니다. 
- `MachineFilter` 일치를 사용 하 여 현재 컴퓨터를 `'.*'` 와일드 카드입니다.
- `AppFilter='WebAppExclude'` 제공 된 `null` InstrumentationKey 합니다. 이 앱을 계측할 수 없습니다.
- `AppFilter='WebAppOne'` 이 특정 응용 프로그램을 고유 계측 키를 할당 됩니다.
- `AppFilter='WebAppTwo'` 이 특정 응용 프로그램 고유 계측 키를 할당할 수도 됩니다.
- 마지막으로, `AppFilter` 사용을 `'.*'` 와일드 카드 일치 다른 모든 web apps를 이전 규칙에 의해 일치 없고 기본 계측 키를 할당 합니다.
- 만 가독성을 위해 추가 공간입니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>매개 변수 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수** 이 매개 변수를 사용 하 여 대상 컴퓨터의 모든 응용 프로그램에서 사용 하기 위해 단일 iKey를 제공 합니다.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수** 이 매개 변수를 사용 하 여 여러 ikey 및 ikey를 사용 하는 앱의 매핑을 제공 합니다. MachineFilter를 설정 하 여 여러 컴퓨터에 대 한 단일 설치 스크립트를 만들 수 있습니다. 

> [!IMPORTANT] 
> 응용 프로그램을 제공 하는 순서에서는 규칙에 대해 일치 합니다. 먼저 가장 구체적인 규칙을 지정 해야는 같이 및 가장 일반적인 규칙입니다.

#### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** vm 또는 컴퓨터 이름의 필요한 c# regex는 합니다.
    - '. *'는 모두 일치
    - 'ComputerName' 똑같은 이름 사용 하 여 컴퓨터에만 일치 합니다.
- **AppFilter** vm 또는 컴퓨터 이름의 필요한 c# regex는 합니다.
    - '. *'는 모두 일치
    - 'ApplicationName' 똑같은 이름 사용 하 여 IIS 응용 프로그램만 일치 합니다.
- **InstrumentationKey** 위의 두 필터와 일치 하는 응용 프로그램의 모니터링을 사용 하도록 설정 해야 합니다.
    - 이 값을 모니터링을 제외 하려면 규칙을 정의 하려는 경우에 null 유지


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**선택** 이벤트 및 관리 되는 프로세스 실행 중에 발생 하는 메시지를 수집 하려면 계측 엔진을 사용 하도록 설정 하려면이 스위치를 사용 합니다. 종속성 결과 코드, HTTP 동사 및 SQL 명령 텍스트에 제한 되지 않고 포함 됩니다. 계측 추가 오버 헤드가 추가 엔진과 기본적으로 해제 되어 있습니다.

### <a name="-acceptlicense"></a>-AcceptLicense
**선택** 헤드리스 설치에서 라이선스 및 개인정보취급방침 적용할이 스위치를 사용 합니다.

### <a name="-verbose"></a>-Verbose
**일반적인 매개 변수입니다.** 자세한 로그를 출력 하려면이 스위치를 사용 합니다.

### <a name="-whatif"></a>-WhatIf 
**일반적인 매개 변수입니다.** 이 스위치를 사용 하 여 테스트를 실제로 모니터링을 사용 하지 않고 입력된 매개 변수 유효성 검사 합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-a-successful-enablement"></a>성공적인 사용의 예제 출력

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
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
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

