---
title: Azure Monitor에서 에이전트 상태 솔루션 Microsoft Docs
description: 이 문서는 Log Analytics 또는 System Center Operations Manager에 직접 보고하는 에이전트의 상태를 모니터링하기 위해 이 솔루션을 사용하는 방법을 쉽게 이해할 수 있도록 해줍니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/19/2017
ms.openlocfilehash: 5a48bbff89f0d6a0be9adf2ad242dbca41eec6db
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555327"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Azure Monitor의 에이전트 상태 솔루션
Azure의 에이전트 상태 솔루션은 Azure Monitor의 Log Analytics 작업 영역에 직접 보고 하는 모든 에이전트 또는 Azure Monitor에 연결 된 System Center Operations Manager 관리 그룹 (응답 하지 않음)에 대해 이해 하는 데 도움이 됩니다. 운영 데이터를 제출 합니다.  또한 얼마나 많은 에이전트가 배포되었는지, 이들 에이전트가 지리적으로 어디에 분산되어 있는지 추적할 수 있으며, Azure, 기타 클라우드 환경 또는 온-프레미스에 배포된 에이전트의 분산 상태를 파악하기 위해 다른 쿼리를 수행할 수 있습니다.    

## <a name="prerequisites"></a>전제 조건
이 솔루션을 배포하기 전에 [Windows 에이전트](../../log-analytics/log-analytics-windows-agent.md)가 Log Analytic 작업 영역 또는 작업 영역에 통합된 [Operations Manager 관리 그룹](../../azure-monitor/platform/om-agents.md)에 보고하도록 지원하는지 확인합니다.

## <a name="solution-components"></a>솔루션 구성 요소
이 솔루션은 작업 영역 및 직접 연결된 에이전트 또는 Operations Manager와 연결된 관리 그룹에 추가되는 다음 리소스로 구성됩니다.

### <a name="management-packs"></a>관리 팩
System Center Operations Manager 관리 그룹이 Log Analytics 작업 영역에 연결된 경우 다음 관리 팩이 Operations Manager에 설치됩니다.  이 솔루션을 추가한 후 직접 연결된 Windows 컴퓨터에 이러한 관리 팩도 함께 설치됩니다. 이러한 관리 팩과 관련하여 아무 것도 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 상태 평가 직접 채널 인텔리전스 팩(Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor 상태 평가 서버 채널 인텔리전스 팩(Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](../../azure-monitor/platform/om-agents.md)을 참조하세요.

## <a name="configuration"></a>구성
[솔루션 추가](solutions.md)에 설명된 프로세스를 사용하여 에이전트 상태 솔루션을 Log Analytics 작업 영역에 추가합니다. 추가 구성은 필요 없습니다.


## <a name="data-collection"></a>데이터 수집
### <a name="supported-agents"></a>지원되는 에이전트
다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 | yes | 하트비트 이벤트는 Windows 에이전트에서 직접 수집됩니다.|
| System Center Operations Manager 관리 그룹 | yes | 하트 비트 이벤트는 관리 그룹에 보고 하는 에이전트에서 60 초 마다 수집 된 다음 Azure Monitor 전달 됩니다. Operations Manager 에이전트에서 Azure Monitor로 직접 연결 하는 것은 필요 하지 않습니다. 하트 비트 이벤트 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달 됩니다.|

## <a name="using-the-solution"></a>솔루션 사용
솔루션을 Log Analytics 작업 영역에 추가하면 대시보드에 **에이전트 상태** 타일이 추가됩니다. 이 타일은 총 에이전트 수와 지난 24시간 동안 응답하지 않는 에이전트의 수를 표시합니다.<br><br> ![대시보드의 에이전트 상태 솔루션 타일](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

**에이전트 상태**타일을 클릭하여 **에이전트 상태** 대시보드를 엽니다.  대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 열 개의 이벤트를 나열합니다. 각 열의 오른쪽 아래쪽에 있는 **모두 보기**를 선택하거나 열 제목을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| 열 | 설명 |
|--------|-------------|
| 시간에 따른 에이전트 수 | Linux 및 Windows 에이전트에 대해 7일 동안의 에이전트 수의 추세입니다.|
| 응답하지 않는 에이전트 개수 | 지난 24시간 동안 하트비트를 보내지 않은 에이전트의 목록입니다.|
| OS 형식별 배포 | 사용자 환경에 있는 Windows 및 Linux 에이전트 개수에 따른 분할입니다.|
| 에이전트 버전별 배포 | 사용자 환경에 설치 된 다른 에이전트 버전의 분할과 그 개수입니다.|
| 에이전트 범주별 배포 | 직접 에이전트, OpsMgr 에이전트 또는 OpsMgr 관리 서버 등 하트비트 이벤트를 전송하는 에이전트의 다양한 범주의 분할입니다.|
| 관리 그룹별 배포 | 사용자 환경에 있는 다른 Operations Manager 관리 그룹의 파티션입니다.|
| 에이전트의 지리적 위치 | 에이전트가 있는 다양 한 국가/지역의 파티션과 각 국가/지역에 설치 된 에이전트 수의 총 개수입니다.|
| 설치된 게이트웨이 개수 | Log Analytics 게이트웨이가 설치되어 있는 서버 수 및 해당 서버의 목록입니다.|

![에이전트 상태 솔루션 대시보드 타일](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor 로그 레코드
솔루션은 Log Analytics 작업 영역에서 하나의 레코드 형식을 만듭니다.  

### <a name="heartbeat-records"></a>하트비트 레코드
**하트비트** 형식이 포함된 레코드가 만들어집니다.  이러한 레코드는 다음 표의 속성을 가집니다.  

| 자산 | 설명 |
| --- | --- |
| `Type` | *하트비트*|
| `Category` | 값은 *직접 에이전트*, *SCOM 에이전트* 또는 *SCOM 관리 서버*합니다.|
| `Computer` | 컴퓨터 이름입니다.|
| `OSType` | Windows 또는 Linux 운영 체제입니다.|
| `OSMajorVersion` | 운영 체제의 주 버전입니다.|
| `OSMinorVersion` | 운영 체제의 부 버전입니다.|
| `Version` | Log Analytics 에이전트 또는 Operations Manager 에이전트 버전입니다.|
| `SCAgentChannel` | 값은 *직접* 및/또는 *SCManagementServer*합니다.|
| `IsGatewayInstalled` | Log Analytics 게이트웨이가 설치된 경우 값은 *true*이거나, 그렇지 않으면 *false*입니다.|
| `ComputerIP` | 컴퓨터의 IP 주소입니다.|
| `RemoteIPCountry` | 컴퓨터가 배포된 지리적 위치입니다.|
| `ManagementGroupName` | Operations Manager 관리 그룹의 이름입니다.|
| `SourceComputerId` | 컴퓨터의 고유 ID입니다.|
| `RemoteIPLongitude` | 컴퓨터 지리적 위치의 경도입니다.|
| `RemoteIPLatitude` | 컴퓨터 지리적 위치의 위도입니다.|

Operations Manager management server에 보고 하는 각 에이전트는 두 하트 비트를 보내고 SCAgentChannel 속성의 값은 보유 한 데이터 원본 및 모니터링 솔루션에 따라 **Direct** 및 **SCManagementServer** 를 모두 포함 합니다. 구독에서 사용할 수 있습니다. 기억 하는 경우 솔루션의 데이터는 Operations Manager management 서버에서 Azure Monitor로 직접 전송 되거나 에이전트에 수집 된 데이터의 양에 따라 에이전트에서 Azure Monitor으로 직접 전송 됩니다. **SCManagementServer** 값이 있는 하트비트 이벤트의 경우, ComputerIP 값은 관리 서버가 데이터를 실제로 업로드하기 때문에 관리 서버의 IP 주소가 됩니다.  SCAgentChannel가 **직접**에 설정된 하트비트의 경우 이 값은 에이전트의 공용 IP 주소입니다.  

## <a name="sample-log-searches"></a>샘플 로그 검색
다음 테이블은 이 솔루션에 의해 수집된 레코드에 대한 샘플 로그 검색을 제공합니다.

| 쿼리 | 설명 |
|:---|:---|
| Heartbeat &#124; distinct Computer |에이전트의 총수 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |지난 24시간 동안 응답하지 않는 에이전트 개수 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |지난 15분 동안 응답하지 않는 에이전트 개수 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |컴퓨터 온라인(지난 24시간) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |지난 30분 동안 총 에이전트 오프라인(지난 24시간 동안) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |시간에 따른 OSType별 에이전트 수의 추세 가져오기|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |OS 형식별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |에이전트 버전별 배포 |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |에이전트 범주별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | 관리 그룹별 배포 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |에이전트의 지리적 위치 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |설치된 Log Analytics 게이트웨이 수 |




## <a name="next-steps"></a>다음 단계

* 로그 쿼리에서 경고를 생성 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 경고](../platform/alerts-overview.md) 를 참조 하세요. 
