---
title: 윈도우 가상 데스크톱 진단 로그 분석 - Azure
description: Windows 가상 데스크톱 진단 기능으로 로그 분석을 사용하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127935"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>진단 기능에 로그 분석 사용

Windows Virtual Desktop은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있는 진단 기능을 제공합니다. 이 기능은 할당된 Windows 가상 데스크톱 역할이 서비스를 사용할 때마다 진단 정보를 기록합니다. 각 로그에는 활동에 관련된 Windows Virtual Desktop 역할에 대한 정보, 세션 중에 나타나는 오류 메시지, 테넌트 정보 및 사용자 정보가 포함됩니다. 진단 기능은 사용자 및 관리 작업 모두에 대한 활동 로그를 만듭니다. 각 활동 로그는 다음 세 가지 주요 범주에 속합니다. 

- 피드 구독 활동: 사용자가 Microsoft 원격 데스크톱 응용 프로그램을 통해 피드에 연결하려고 할 때
- 연결 활동: 사용자가 Microsoft 원격 데스크톱 응용 프로그램을 통해 데스크톱 또는 RemoteApp에 연결하려고 할 때
- 관리 활동: 관리자가 호스트 풀 만들기, 앱 그룹에 사용자 할당, 역할 할당 만들기 와 같은 시스템에서 관리 작업을 수행하는 경우

진단 역할 서비스 자체가 Windows 가상 데스크톱의 일부이므로 Windows 가상 데스크톱에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. Windows 가상 데스크톱 연결 문제는 사용자가 네트워크 연결 문제가 발생할 때 발생할 수 있습니다.

## <a name="why-you-should-use-log-analytics"></a>로그 애널리틱스를 사용해야 하는 이유

로그 애널리틱스를 사용하여 단일 사용자 문제 해결을 넘어서는 Azure 클라이언트의 진단 데이터를 분석하는 것이 좋습니다. 로그 애널리틱스에 VM 성능 카운터를 가져올 수 있으므로 배포에 대한 정보를 수집하는 도구가 하나 있습니다.

## <a name="before-you-get-started"></a>시작하기 전에

진단 기능과 함께 Log Analytics를 사용하려면 하려면 작업 [영역을 만들어야](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)합니다.

작업 영역을 만든 후 Windows 컴퓨터 [연결의](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) 지침에 따라 Azure 모니터에 다음 정보를 가져옵니다. 

- 작업 영역 ID
- 작업 영역의 기본 키

설정 프로세스의 나중에 이 정보가 필요합니다.

## <a name="push-diagnostics-data-to-your-workspace"></a>진단 데이터를 작업 공간으로 푸시 

Windows 가상 데스크톱 테넌트의 진단 데이터를 작업 영역에 대한 로그 분석으로 푸시할 수 있습니다. 작업 영역을 테넌트에 연결하여 테넌트를 처음 만들 때 이 기능을 즉시 설정하거나 나중에 기존 테넌트를 사용하여 설정할 수 있습니다.

새 테넌트를 설정하는 동안 테넌트를 Log Analytics 작업 영역에 연결하려면 다음 cmdlet을 실행하여 TenantCreator 사용자 계정으로 Windows 가상 데스크톱에 로그인합니다. 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

새 테넌트 대신 기존 테넌트를 연결하려는 경우 이 cmdlet을 대신 실행합니다. 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

로그 애널리틱스에 연결하려는 모든 테넌트에 대해 이러한 cmdlet을 실행해야 합니다. 

>[!NOTE]
>테넌트를 만들 때 Log Analytics 작업 영역을 연결하지 않으려면 대신 cmdlet을 `New-RdsTenant` 실행합니다. 

## <a name="cadence-for-sending-diagnostic-events"></a>진단 이벤트를 보내는 케이던스

진단 이벤트가 완료되면 로그 분석으로 전송됩니다.  

## <a name="example-queries"></a>쿼리 예

다음 예제 쿼리는 진단 기능이 시스템에서 가장 빈번한 활동에 대한 보고서를 생성하는 방법을 보여 줍니다.

이 첫 번째 예제에서는 지원되는 원격 데스크톱 클라이언트를 사용하는 사용자가 시작한 연결 활동을 보여 주며 있습니다.

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

다음 예제 쿼리는 테넌에 대한 관리자의 관리 활동을 보여 주었습니다.

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>로그 애널리틱스로 데이터 전송 중지 

기존 테넌트에서 Log Analytics로 데이터를 전송하지 않도록 하려면 다음 cmdlet을 실행하고 빈 문자열을 설정합니다.

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

데이터 전송을 중지하려는 모든 테넌트에 대해 이 cmdlet을 실행해야 합니다. 

## <a name="next-steps"></a>다음 단계 

진단 기능이 식별할 수 있는 일반적인 오류 시나리오를 검토하려면 [문제 식별 및 진단을](diagnostics-role-service.md#common-error-scenarios)참조하십시오.
