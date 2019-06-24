---
title: Windows에 대 한 Log Analytics 에이전트를 사용 하 여 문제를 해결 하는 방법 | Microsoft Docs
description: Azure Monitor에서 Windows에 대 한 Log Analytics 에이전트를 사용 하 여 증상, 원인 및 가장 일반적인 문제에 대 한 확인을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120111"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Windows에 대 한 Log Analytics 에이전트를 사용 하 여 문제를 해결 하는 방법 

이 문서에서는 Azure Monitor에서 Windows에 대 한 Log Analytics 에이전트를 사용 하 여 발생할 수 및 가능한 해결 방법을 제시 하는 오류 문제 해결 도움말을 제공 합니다.

이 문서의 단계를 수행해도 문제가 해결되지 않으면 다음 지원 채널을 사용할 수 있습니다.

* 프리미어 지원 혜택을 받는 고객은 [프리미어](https://premier.microsoft.com/)를 사용하여 지원 요청을 열 수 있습니다.
* Azure 지원 계약을 맺은 고객은 [Azure Portal](https://manage.windowsazure.com/?getsupport=true)에서 지원 요청을 열 수 있습니다.
* 제출된 아이디어와 버그를 검토하거나 새로운 아이디어를 제출하려면 Log Analytics 피드백 페이지([https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback))를 방문하세요. 

## <a name="important-troubleshooting-sources"></a>중요 한 문제 해결 원본

 Windows에 대 한 Log Analytics 에이전트와 관련 된 문제 해결을 돕기 위해 에이전트 이벤트를 기록 Windows 이벤트 로그에 아래에서 특별히 *응용 프로그램 및 Services\Operations Manager*합니다.  

## <a name="connectivity-issues"></a>연결 문제

에이전트 프록시 서버나 방화벽을 통해 통신 하는 경우 원본 컴퓨터와 Azure Monitor 서비스에서 통신을 방해 하는 위치에 제한 사항이 있을 수 있습니다. 통신은 차단 하는 경우 잘못 된 구성, 작업 영역을 사용 하 여 등록 실패할 수 있습니다 보고 작업 영역을 추가 하려면 설치 후 에이전트를 구성 에이전트를 설치 하려고 하거나 성공적인 등록 후 에이전트 간 통신에 실패 하는 동안. 이 섹션에서는 이러한 종류의 Windows 에이전트를 사용 하 여 문제를 해결 하는 방법을 설명 합니다. 

방화벽 또는 프록시는 다음 포트와 다음 표에 설명 된 Url을 허용 하도록 구성 되어 있는지 다시 확인 합니다. 또한 Azure Monitor와 에이전트 간의 TLS 보안 채널을 방지할 수 있습니다 하는 대로 웹 트래픽에 대 한 HTTP 검사 가능 하지 확인 합니다.  

|에이전트 리소스|포트 |Direction |HTTPS 검사 무시|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |포트 443 |아웃바운드|예 |  
|*.oms.opinsights.azure.com |포트 443 |아웃바운드|예 |  
|\*.blob.core.windows.net |포트 443 |아웃바운드|예 |  
|\* .azure-automation.net |포트 443 |아웃바운드|예 |  

Azure Government에 대 한 필요한 방화벽 정보를 참조 하세요 [Azure Government 관리](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs)합니다. 

여러 가지 방법으로 Azure Monitor를 사용 하 여 에이전트를 성공적으로 통신 하는 경우를 확인할 수 있습니다.

- 사용 하도록 설정 합니다 [Azure Log Analytics 에이전트 상태 평가](../insights/solution-agenthealth.md) 작업 영역에서 합니다. 에이전트 상태 대시보드에서 보기를 **응답 하지 않는 에이전트 개수** 열을 신속 하 게 하는 경우 해당 에이전트가 표시 됩니다.  

- 에이전트에 보고 하도록 구성 된 작업 영역에 하트 비트를 보내는 확인 하려면 다음 쿼리를 실행 합니다. 대체 <ComputerName> 컴퓨터의 실제 이름입니다.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    서비스와 통신 하는 컴퓨터는 성공적으로 경우 쿼리 결과 반환 해야 합니다. 쿼리 결과 반환 하지 않은 경우 먼저 에이전트가 올바른 작업 영역에 보고 하도록 구성 된 확인 합니다. 올바르게 구성 된 경우 3 단계로 진행 하 고 어떤 문제를 방해할 수 고 Azure Monitor를 사용 하 여 통신 하는 에이전트 기록 하는 경우 식별 하기 위해 Windows 이벤트 로그를 검색 합니다.

- 실행 된 경우 연결 문제를 식별 하는 또 다른 방법은 합니다 **TestCloudConnectivity** 도구입니다. 도구 폴더에서 에이전트를 사용 하 여 기본적으로 설치 됩니다 *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*합니다. 관리자 권한 명령 프롬프트에서 폴더로 이동 하 고 도구를 실행 합니다. 도구에는 결과 및 주요 내용 (예를 들어 경우 특정 포트/URL이 차단 되는 관련 된) 테스트가 실패 하는 위치를 반환 합니다. 

    ![TestCloudConnection 도구 실행 결과](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- 필터를 *Operations Manager* 이벤트 로그 **이벤트 원본** - *Health Service 모듈*를 *HealthService*, 및 *Service Connector* 기준으로 필터링 하 고 **이벤트 수준** *경고* 하 고 *오류* 이벤트를 기록 하는 경우를 확인 하는 다음 테이블입니다. 경우에 가능한 각 이벤트에 대 한 포함 된 해결 단계를 검토 합니다.

    |이벤트 ID |source |설명 |해결 방법 |
    |---------|-------|------------|-----------|
    |2133 & 2129 |상태 관리 서비스 |에이전트에서 서비스에 연결 하지 못했습니다. |에이전트는 직접 또는 Azure Monitor 서비스에 방화벽/프록시 서버를 통해 통신할 수 없는 경우이 오류가 발생할 수 있습니다. 에이전트 프록시 설정이 나 네트워크 방화벽/프록시 서비스에 컴퓨터에서 TCP 트래픽을 허용 하는지 확인 합니다.|
    |2138 |상태 관리 서비스 모듈 |프록시 인증 필요 |에이전트 프록시 설정을 구성 하 고 프록시 서버를 사용 하 여 인증 하는 데 필요한 사용자 이름/암호를 지정 합니다. |
    |2129 |상태 관리 서비스 모듈 |실패 한 연결/SSL 협상이 실패 |에이전트 프록시 설정을 확인 하 고 네트워크 어댑터의 TCP/IP 설정을 확인 합니다.|
    |2127 |상태 관리 서비스 모듈 |데이터 전송 실패 오류 코드를 받았습니다. |하루 동안 정기적으로 발생을 하는 경우는 무시할 수 있는 임의 변칙 수만 있습니다. 발생 빈도 이해 하는 모니터입니다. 하루 종일 자주 발생 하는 경우에 네트워크 구성 및 프록시 설정을 확인 먼저 합니다. 404 HTTP 오류 코드에 대 한 설명을 이며 처음 에이전트 서비스에 데이터를 전송 하려고 하는 경우 내부 404 오류 코드 500 오류를 포함 됩니다. 새 작업 영역에 대 한 저장소 영역을 프로비저닝되 고 여전히 나타내는 찾을 수 없으면 404을 의미 합니다. 데이터가 다음 재시도 성공적으로 예상한 대로 작업 영역에 작성 합니다. HTTP 오류 403 사용 권한 또는 자격 증명 문제를 나타낼 수 있습니다. 자세한 내용은 문제를 해결 하는 데 403 오류가 포함 된 경우|
    |4000 |서비스 커넥터 |DNS 이름 확인에 실패 했습니다 |컴퓨터에서 서비스에 데이터를 보낼 때 사용 되는 인터넷 주소를 확인 하지 못했습니다. 이 공급자를 사용 하 여 컴퓨터, 잘못 된 프록시 설정 또는 일시적인 DNS 문제가 있을 수 있습니다의 DNS 확인자 설정 수 있습니다. 주기적으로 발생 하는 경우 일시적인 네트워크 관련 문제로 인해 발생할 수 있습니다.|
    |4001 |서비스 커넥터 |서비스에 연결 하지 못했습니다. |에이전트는 직접 또는 Azure Monitor 서비스에 방화벽/프록시 서버를 통해 통신할 수 없는 경우이 오류가 발생할 수 있습니다. 에이전트 프록시 설정이 나 네트워크 방화벽/프록시 서비스에 컴퓨터에서 TCP 트래픽을 허용 하는지 확인 합니다.|
    |4002 |서비스 커넥터 |서비스 쿼리에 대 한 응답으로 HTTP 상태 코드 403을 반환 합니다. 서비스의 상태에 대 한 서비스 관리자를 사용 하 여 확인 합니다. 쿼리는 나중에 다시 시도 됩니다. |에이전트의 초기 등록 단계 동안이 오류가 기록 됩니다 하 고 다음과 유사한 URL을 표시 합니다. *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*합니다. 오류 코드 403 방법을 사용할 수 없습니다 및 작업 영역 ID 또는 키를 잘못 입력 하 여 발생할 수 있습니다 또는 데이터 및 시간을 컴퓨터에 올바르지 않습니다. 시간이 현재 시간에서 +/-15분인 경우 등록이 실패합니다. 이 문제를 해결 하려면 날짜 및/또는 Windows 컴퓨터의 표준 시간대를 업데이트 합니다.|

## <a name="data-collection-issues"></a>데이터 수집 문제

에이전트를 설치 후 구성, 수집 또는 전달 성능, 로그 또는 기타 데이터가 사용할 수에 따라 서비스를 받고 컴퓨터를 대상으로 중지 될 수는 구성 된 작업 영역 또는 작업 영역을 보고서. 확인 해야 하는:

- 특정 데이터 형식 또는 작업 영역에서 사용할 수 없는 모든 데이터 입니까?
- 솔루션에서 지정 하거나 작업 영역 데이터 컬렉션 구성의 일부로 지정 된 데이터 형식입니다.
- 컴퓨터 수는 영향을 받습니까? 단일 또는 여러 대의 컴퓨터 작업 영역에 보고 입니까?
- 작동 된 및 하루 중 특정 시간에 중지 않았습니다 또는 해당 적이 수집? 
- 구문이 정확 하면서 사용 하는 로그 검색 쿼리 기능 
- 에 그 어느 때 해당 및 구성을 받은 에이전트 Azure Monitor에서?

문제 해결 단계는 첫 번째 컴퓨터 하트 비트 이벤트를 보내는 경우를 결정 하는 것입니다.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

쿼리 결과 반환 하는 경우 다음 결정 해야 하는 경우 특정 데이터 형식이 없습니다 수집 되어 서비스로 전달 합니다. 에이전트 서비스 또는 에이전트를 정상적으로 작동 하지 못하도록 방지 하는 다른 일부 증상에서 업데이트 된 구성을 받지 때문일 수 있습니다. 해결 하려면 다음 단계를 수행 합니다.

1. 컴퓨터에서 관리자 권한 명령 프롬프트를 열고를 입력 하 여 에이전트 서비스를 다시 `net stop healthservice && net start healthservice`입니다.
2. 엽니다는 *Operations Manager* 이벤트 로그 및 검색 **이벤트 Id** *7023, 7024, 7025, 7028* 고 *1210* 에서 **이벤트 소스** *HealthService*합니다.  이러한 이벤트에는 에이전트가 성공적으로 Azure Monitor에서 구성을 수신 하 고 컴퓨터를 모니터링 하 고 적극적으로 나타냅니다. 이벤트 설명 ID 1210 마지막으로 지정 하는 이벤트에 대 한 모든 솔루션 및 에이전트에서 모니터링의 범위에 포함 된 Insights 선입니다.  

    ![이벤트 ID 1210 설명](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. 몇 분 후에 예상된 데이터가 쿼리 결과 또는 시각화에 표시 되지 않으면, 경우에 따라 솔루션 또는 정보 데이터로에서 보고 된 *Operations Manager* 이벤트 로그를 검색 **이벤트 원본** *HealthService* 하 고 *Health Service 모듈* 기준으로 필터링 하 고 **이벤트 수준** *경고* 및 *오류* 다음 테이블에서 이벤트에 기록 하는 경우를 확인 합니다.

    |이벤트 ID |source |설명 |해결 방법 |
    |---------|-------|------------|
    |8000 |HealthService |이 이벤트는 경우 워크플로 관련 된 성능, 이벤트 또는 수집 된 다른 데이터 형식에 대 한 작업 영역에 수집 하 고 서비스로 전달할 수 없는 지정 합니다. | 이벤트 ID 2136 원본의 health service가이 이벤트와 함께 기록 되 고 에이전트를 나타낼 수 있습니다 프록시 및 인증 설정, 네트워크 중단 또는 네트워크 방화벽의 잘못 된 구성 때문일 서비스와 통신할 수 없는 / 프록시는 컴퓨터에서 서비스의 TCP 트래픽을 허용 하지 않습니다.| 
    |10102 및 10103 |상태 관리 서비스 모듈 |워크플로 데이터 소스를 확인 하지 못했습니다. |지정한 성능 카운터 또는 인스턴스를 컴퓨터에 존재 하지 않거나 데이터 작업 영역 설정에 잘못 정의 하는 경우 발생할 수 있습니다. 사용자 지정 된 경우 [성능 카운터](data-sources-performance-counters.md#configuring-performance-counters)에서 지정 된 정보는 올바른 형식에 따라가 확인 하 고 대상 컴퓨터에 존재 합니다. |
    |26002 |상태 관리 서비스 모듈 |워크플로 데이터 소스를 확인 하지 못했습니다. |지정된 된 Windows 이벤트 로그를 컴퓨터에 없는 경우 발생할 수 있습니다. 컴퓨터 등록, 그렇지 않으면 사용자 지정 된 경우이 이벤트 로그으로 예상 되지 않는 경우이 오류 안전 하 게 무시할 수 있습니다 [이벤트 로그](data-sources-windows-events.md#configuring-windows-event-logs), 지정 된 정보가 올바른지 확인 합니다. |

