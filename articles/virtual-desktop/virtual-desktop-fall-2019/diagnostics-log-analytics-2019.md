---
title: Azure Virtual Desktop(클래식) 진단 로그 분석 - Azure
description: Azure Virtual Desktop(클래식) 진단 기능과 함께 로그 분석을 사용하는 방법.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0be29603225361b2f275f081c1ad52c5c6c394db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566762"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop(클래식)의 진단 기능에 Log Analytics 사용

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../diagnostics-log-analytics.md)를 참조하세요.

Azure Virtual Desktop은 관리자가 단일 인터페이스를 통해 문제를 식별할 수 있도록 지원하는 진단 기능을 제공합니다. 이 기능은 Azure Virtual Desktop 역할이 할당된 사용자가 서비스를 사용할 때마다 진단 정보를 기록합니다. 각 로그에는 작업과 관련된 Azure Virtual Desktop 역할에 대한 정보, 세션 중에 표시되는 오류 메시지, 테넌트 정보 및 사용자 정보가 포함됩니다. 진단 기능은 사용자 및 관리 작업 모두에 대해 활동 로그를 만듭니다. 각 활동 로그는 세 가지 주요 범주로 구분됩니다.

- 피드 구독 활동: 사용자가 Microsoft 원격 데스크톱 애플리케이션을 통해 피드에 연결하려고 시도하는 경우입니다.
- 연결 작업: 사용자가 Microsoft 원격 데스크톱 애플리케이션을 통해 데스크톱 또는 RemoteApp에 연결하려고 시도하는 경우입니다.
- 관리 작업: 관리자가 시스템에서 호스트 풀 만들기, 앱 그룹에 사용자 할당, 역할 할당 만들기와 같은 관리 작업을 수행하는 경우입니다.

진단 역할 서비스 자체가 Azure Virtual Desktop의 일부이므로 Azure Virtual Desktop에 도달하지 않는 연결은 진단 결과에 표시되지 않습니다. Azure Virtual Desktop 연결 문제는 사용자가 네트워크 연결 문제를 경험할 때 발생할 수 있습니다.

## <a name="why-you-should-use-log-analytics"></a>Log Analytics를 사용해야 하는 이유

Log Analytics를 사용하여 단일 사용자 문제 해결을 넘어 Azure 클라이언트의 진단 데이터를 분석하는 것이 좋습니다. VM 성능 카운터를 Log Analytics로 끌어올 수 있으므로 배포에 대한 정보를 수집하는 도구가 하나 있습니다.

## <a name="before-you-get-started"></a>시작하기 전에

진단 기능과 함께 Log Analytics를 사용하려면 먼저 [작업 영역을 만들](../../azure-monitor/logs/quick-create-workspace.md)어야 합니다.

작업 영역을 만든 후 [Azure Monitor에 Windows 컴퓨터 연결](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)의 지침에 따라 다음 정보를 가져옵니다.

- 작업 영역 ID
- 작업 영역의 기본 키

이 정보는 나중에 설정 프로세스에서 필요합니다.

## <a name="push-diagnostics-data-to-your-workspace"></a>작업 영역에 진단 데이터 푸시

Azure Virtual Desktop 테넌트에서 작업 영역의 Log Analytics로 진단 데이터를 푸시할 수 있습니다. 작업 영역을 테넌트에 연결하여 테넌트를 처음 만들 때 이 기능을 즉시 설정하거나 나중에 기존 테넌트를 사용하여 설정할 수 있습니다.

새 테넌트를 설정하는 동안 Log Analytics 작업 영역에 테넌트를 연결하려면 다음 cmdlet을 실행하여 TenantCreator 사용자 계정으로 Azure Virtual Desktop에 로그인합니다.

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

새 테넌트 대신 기존 테넌트를 연결하려는 경우 대신 다음 cmdlet을 실행합니다.

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Log Analytics에 연결하려는 모든 테넌트에 대해 이러한 cmdlet을 실행해야 합니다.

>[!NOTE]
>테넌트를 만들 때 Log Analytics 작업 영역을 연결하지 않으려면 대신 `New-RdsTenant` cmdlet을 실행합니다.

## <a name="cadence-for-sending-diagnostic-events"></a>진단 이벤트 전송 주기

완료되었으면 진단 이벤트가 Log Analytics로 전송됩니다.

## <a name="example-queries"></a>쿼리 예

다음 예제 쿼리는 진단 기능이 시스템에서 가장 자주 수행되는 작업에 대한 보고서를 생성하는 방법을 보여줍니다.

다음 첫 번째 예제에서는 지원되는 원격 데스크톱 클라이언트가 있는 사용자가 시작한 연결 작업을 보여줍니다.

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

다음 예제 쿼리는 테넌트의 관리자에 의한 관리 활동을 보여줍니다.

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

## <a name="stop-sending-data-to-log-analytics"></a>Log Analytics에 데이터 전송 중지

기존 테넌트에서 Log Analytics로 데이터 전송을 중지하려면 다음 cmdlet을 실행하고 빈 문자열을 설정합니다.

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

데이터 보내기를 중지하려는 모든 테넌트에 대해 이 cmdlet을 실행해야 합니다.

## <a name="next-steps"></a>다음 단계

진단 기능으로 사용자를 식별할 수 있는 일반적인 오류 시나리오를 검토하려면 [ID 및 진단 문제](diagnostics-role-service-2019.md#common-error-scenarios)를 참조하세요.
