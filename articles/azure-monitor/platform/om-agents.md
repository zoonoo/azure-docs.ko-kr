---
title: 운영 관리자를 Azure 모니터에 연결 | 마이크로 소프트 문서
description: System Center Operations Manager의 기존 투자를 유지 관리하고 Log Analytics로 확장된 기능을 사용하려면 작업 영역으로 Operations Manager를 통합할 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274347"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>운영 관리자를 Azure 모니터에 연결

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

시스템 센터 운영 [관리자에](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) 대한 기존 투자를 유지하고 Azure Monitor에서 확장 기능을 사용하려면 운영 관리자를 Log Analytics 작업 영역과 통합할 수 있습니다. 이를 통해 운영 관리자를 계속 사용하여 Azure Monitor에서 로그 영업 기회를 활용할 수 있습니다.

* Operations Manager를 사용하여 IT 서비스의 상태 모니터링
* 인시던트 및 문제 관리를 지원하는 ITSM 솔루션과 통합 유지 관리
* 온-프레미스 및 Operations Manager로 모니터링하는 퍼블릭 클라우드 IaaS 가상 머신에 배포된 에이전트의 수명 주기 관리

시스템 센터 운영 관리자와 통합하면 운영 관리자에서 로그 데이터를 수집, 저장 및 분석할 때 Azure Monitor의 속도와 효율성을 사용하여 서비스 운영 전략에 가치를 더합니다. Azure Monitor 로그 쿼리는 문제의 오류를 식별하고 기존 문제 관리 프로세스를 지원하기 위해 되풀이를 식별하는 데 도움이 됩니다. 풍부한 대시보드 및 보고 기능을 통해 성능, 이벤트 및 경고 데이터를 검사하는 쿼리 엔진의 유연성은 이 데이터를 의미 있는 방식으로 노출할 수 있도록 Azure Monitor가 운영 관리자를 보완하는 데 미치는 강점을 보여줍니다.

Operations Manager 관리 그룹에 보고하는 에이전트는 작업 영역에서 활성화한 [Log Analytics 데이터 원본](agent-data-sources.md) 및 솔루션을 기반으로 서버에서 데이터를 수집합니다. 활성화된 솔루션에 따라 해당 데이터는 Operations Manager 관리 서버에서 서비스로 직접 전송되거나 에이전트 관리 시스템에서 수집된 데이터의 양으로 인해 에이전트에서 Log Analytics 작업 영역으로 직접 전송됩니다. 관리 서버는 데이터를 서비스에 직접 전달하며, 운영 또는 데이터 웨어하우스 데이터베이스에 기록되지 않습니다. 관리 서버가 Azure Monitor와의 연결이 끊어지면 통신이 다시 설정될 때까지 데이터를 로컬로 캐시합니다. 계획된 유지 관리 또는 계획되지 않은 중단으로 인해 관리 서버가 오프라인 상태가 되면 관리 그룹의 다른 관리 서버가 Azure Monitor와의 연결을 다시 시작합니다.  

다음 다이어그램은 방향 및 포트를 포함하여 시스템 센터 운영 관리자 관리 그룹과 Azure Monitor의 관리 서버와 에이전트 간의 연결을 보여 주었습니다.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

IT 보안 정책이 네트워크의 컴퓨터가 인터넷에 연결하도록 허용하지 않을 경우 Log Analytics 게이트웨이에 연결하여 구성 정보를 받고 사용하도록 설정한 솔루션에 따라 수집된 데이터를 보내도록 관리 서버를 구성할 수 있습니다. 로그 분석 게이트웨이를 통해 Azure Monitor로 통신하도록 Operations Manager 관리 그룹을 구성하는 방법에 대한 자세한 정보 및 단계는 [로그 분석 게이트웨이를 사용하여 Azure 모니터에 컴퓨터 연결을](../../azure-monitor/platform/gateway.md)참조하세요.  

## <a name="prerequisites"></a>사전 요구 사항

시작에 앞서 다음 요구 사항을 검토합니다.

* Azure Monitor는 시스템 센터 운영 관리자 2016 이상, 운영 관리자 2012 SP1 UR6 이상, 운영 관리자 2012 R2 UR2 이상만 지원합니다. 프록시 지원은 Operations Manager 2012 SP1 UR7 및 Operations Manager 2012 R2 UR3에 추가되었습니다.
* 시스템 센터 운영 관리자 2016을 미국 정부 클라우드와 통합하려면 업데이트 롤업 2 이상에 포함된 업데이트된 Advisor 관리 팩이 필요합니다. 시스템 센터 운영 관리자 2012 R2에는 업데이트 롤업 3 이상에 포함된 업데이트된 어드바이저 관리 팩이 필요합니다.
* 모든 Operations Manager 에이전트는 최소 지원 요구 사항을 만족해야 합니다. 에이전트가 최소 업데이트를 따르고 있는지 확인하고, 그렇지 않으면 Windows 에이전트 통신이 실패하고 Operations Manager 이벤트 로그에 오류가 생성될 수 있습니다.
* Log Analytics 작업 영역. 자세한 내용은 [Log Analytics 작업 영역 개요](design-logs-deployment.md)를 검토합니다. 
* [Log Analytics 기여자 역할](manage-access.md#manage-access-using-azure-permissions)의 멤버인 계정으로 Azure에 인증합니다.

* 지원되는 지역 - 시스템 센터 운영 관리자에서 다음 Azure 리전만 지원하여 Log Analytics 작업 영역에 연결합니다.
    - 미국 중서부
    - 오스트레일리아 동남부
    - 서유럽
    - 미국 동부
    - 동남아시아
    - 일본 동부
    - 영국 남부
    - 인도 중부
    - 캐나다 중부
    - 미국 서부 2

>[!NOTE]
>Azure API에 대한 최근 변경으로 인해 고객이 처음으로 관리 그룹과 Azure Monitor 간의 통합을 성공적으로 구성할 수 없습니다. 관리 그룹과 서비스를 이미 통합한 고객의 경우, 기존 연결을 재구성해야 하는 경우가 아니면 영향을 받지 않습니다.  
>다음 버전의 Operations Manager에 대해 새로운 관리 팩이 릴리스되었습니다.
> - 시스템 센터 운영 관리자 2019의 경우 이 관리 팩은 원본 미디어에 포함되며 새 관리 그룹을 설정하는 동안 또는 업그레이드 중에 설치됩니다.
>- 운영 관리자 1801 관리 팩은 운영 관리자 1807에도 적용됩니다.
>- 시스템 센터 운영 관리자 1801의 경우 여기에서 관리 팩을 [다운로드하십시오.](https://www.microsoft.com/download/details.aspx?id=57173)
>- 시스템 센터 2016 - 운영 관리자의 경우 여기에서 관리 팩을 [다운로드하십시오.](https://www.microsoft.com/download/details.aspx?id=57172)  
>- 시스템 센터 운영 관리자 2012 R2의 경우 여기에서 관리 팩을 [다운로드하십시오.](https://www.microsoft.com/download/details.aspx?id=57171)  


### <a name="network"></a>네트워크

아래 정보는 Azure Monitor와 통신하는 데 필요한 작업 관리자 에이전트, 관리 서버 및 운영 콘솔에 필요한 프록시 및 방화벽 구성 정보를 나열합니다. 각 구성 요소의 트래픽은 네트워크에서 Azure Monitor로 아웃바운드됩니다.

|리소스 | 포트 번호| HTTP 검사 무시|  
|---------|------|-----------------------|  
|**에이전트**|||  
|\*.ods.opinsights.azure.com| 443 |yes|  
|\*.oms.opinsights.azure.com| 443|yes|  
|\*.blob.core.windows.net| 443|yes|  
|\*.azure-automation.net| 443|yes|  
|**관리 서버**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| yes|  
|\*.ods.opinsights.azure.com| 443| yes|  
|* .azure-automation.net | 443| yes|  
|**Azure 모니터에 대한 운영 관리자 콘솔**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 및 443||  
|\*.microsoft.com| 80 및 443||  
|\*.microsoftonline.com| 80 및 443||  
|\*.mms.microsoft.com| 80 및 443||  
|login.windows.net| 80 및 443||  
|portal.loganalytics.io| 80 및 443||
|api.loganalytics.io| 80 및 443||
|docs.loganalytics.io| 80 및 443||  

### <a name="tls-12-protocol"></a>TLS 1.2 프로토콜

Azure Monitor로 전송되는 데이터의 보안을 보장하기 위해 최소한 TLS(전송 계층 보안) 1.2를 사용하도록 에이전트 및 관리 그룹을 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**. 자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)를 검토하세요.

## <a name="connecting-operations-manager-to-azure-monitor"></a>운영 관리자를 Azure 모니터에 연결

Operations Manager 관리 그룹을 구성하도록 다음과 같은 일련의 단계를 수행하여 Log Analytics 작업 영역 중 하나에 연결합니다.

Log Analytics 작업 영역에 Operations Manager 관리 그룹을 처음으로 등록하는 동안 관리 그룹에 대한 프록시 구성을 지정하는 옵션을 운영 콘솔에서 사용할 수 없습니다.  이 옵션을 사용하려면 관리 그룹이 서비스에 등록되어 있어야 합니다.  이 문제를 해결하려면 운영 콘솔을 실행하는 시스템에서 Netsh를 사용하여 관리 그룹의 통합 및 모든 관리 서버를 구성하여 시스템 프록시 구성을 업데이트해야 합니다.  

1. 관리자 권한 명령 프롬프트를 엽니다.
   a. **시작**으로 이동하여 **cmd**를 입력합니다.
   b. **명령 프롬프트를** 마우스 오른쪽 단추로 클릭하고 관리자로 실행을 선택합니다**.
1. 다음 명령을 입력하고 **Enter** 키를 누릅니다.

    `netsh winhttp set proxy <proxy>:<port>`

Azure Monitor와 통합하기 위해 다음 단계를 완료한 후 `netsh winhttp reset proxy` 실행하여 구성을 제거한 다음 운영 콘솔에서 **프록시 서버 구성** 옵션을 사용하여 프록시 또는 Log Analytics 게이트웨이 서버를 지정할 수 있습니다.

1. Operations Manager 콘솔에서 **관리** 작업 영역을 선택합니다.
1. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
1. **Operations Management Suite에 등록** 링크를 클릭합니다.
1. **Operations Management Suite 등록 마법사: 인증** 페이지에서 OMS 구독과 연결된 관리자 계정의 전자 메일 주소 또는 전화 번호와 암호를 입력하고 **로그인**을 클릭합니다.

   >[!NOTE]
   >운영 관리 제품군 이름이 사용 중지되었습니다.

1. 성공적으로 인증된 후에 **Operations Management Suite 등록 마법사: 작업 영역 선택** 페이지에 Azure 테넌트, 구독 및 Log Analytics 작업 영역을 선택하라는 메시지가 나타납니다. 둘 이상의 작업 영역이 있는 경우 드롭다운 목록에서 Operations Manager 관리 그룹으로 등록하려는 작업 영역을 선택한 후 **다음**을 클릭합니다.

   > [!NOTE]
   > Operations Manager는 한 번에 하나의 Log Analytics 작업 영역만 지원합니다. 이전 작업 영역이 있는 Azure Monitor에 등록된 연결 및 컴퓨터가 Azure 모니터에서 제거됩니다.
   >
   >
1. **Operations Management Suite 등록 마법사: 요약** 페이지에서 설정을 확인하고 올바른 경우 **만들기**를 클릭합니다.
1. **Operations Management Suite 등록 마법사: 마침** 페이지에서 **닫기**를 클릭합니다.

### <a name="add-agent-managed-computers"></a>에이전트 관리 컴퓨터 추가

Log Analytics 작업 영역과 통합을 구성한 후 Log Analytics와의 연결을 설정하고 에이전트 보고에서 관리 그룹으로 데이터가 수집되지 않습니다. 이 작업을 구성할 때까지 Azure Monitor에 대 한 로그 데이터를 수집 하는 특정 에이전트 관리 컴퓨터까지 발생 합니다. 컴퓨터 개체를 개별적으로 선택하거나 Windows 컴퓨터 개체를 포함하는 그룹을 선택할 수 있습니다. 논리 디스크 또는 SQL 데이터베이스와 같은 다른 클래스의 인스턴스를 포함하는 그룹을 선택할 수 없습니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
1. 창 오른쪽 작업 제목 아래의 **컴퓨터/그룹 추가** 링크를 클릭합니다.
1. **컴퓨터 검색** 대화 상자에서, Operations Manager에서 모니터링하는 컴퓨터 또는 그룹을 검색할 수 있습니다. Azure 모니터에 온보온할 운영 관리자 관리 서버를 포함한 컴퓨터 또는 그룹을 선택하고 **추가를**클릭한 다음 **확인을**클릭합니다.

운영 콘솔의 **관리** 작업 영역에서 Operations Management Suite 아래의 관리되는 컴퓨터 노드에서 데이터를 수집하도록 구성된 컴퓨터 및 그룹을 볼 수 있습니다. 여기에서 필요에 따라 컴퓨터 및 그룹을 추가하거나 제거할 수 있습니다.

### <a name="configure-proxy-settings-in-the-operations-console"></a>운영 콘솔에서 프록시 설정 구성

내부 프록시 서버가 관리 그룹과 Azure Monitor 사이에 있는 경우 다음 단계를 수행합니다. 이러한 설정은 관리 그룹에서 중앙에서 관리되며 Azure Monitor에 대한 로그 데이터를 수집하는 범위에 포함된 에이전트 관리 시스템에 분산됩니다.  이는 특정 솔루션이 관리 서버를 우회하고 데이터를 Log Analytics 서비스에 직접 보낼 때 효과적입니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. Operations Management Suite를 확장한 다음 **연결**을 클릭합니다.
1. OMS 연결 보기에서 **프록시 서버 구성**을 클릭합니다.
1. **Operations Management Suite 마법사: 프록시 서버** 페이지에서 **프록시 서버를 사용하여 Operations Management Suite에 액세스**를 선택하고 포트 번호와 함께 URL을 입력(예: http://corpproxy:80 )한 다음, **마침**을 클릭합니다.

프록시 서버에 인증이 필요한 경우 다음 단계를 수행하여 관리 그룹의 Azure Monitor에 보고하는 관리되는 컴퓨터로 전파해야 하는 자격 증명 및 설정을 구성합니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. **RunAs 구성**에서 **프로필**을 선택합니다.
1. **프로필 프록시로 시스템 센터 관리자 실행** 프로필을 엽니다.
1. 실행 프로필 마법사에서 추가를 클릭하여 실행 계정을 사용합니다. [실행 계정](https://technet.microsoft.com/library/hh321655.aspx)을 만들거나 기존 계정을 사용할 수 있습니다. 이 계정에는 프록시 서버를 통과할 수 있는 권한이 있어야 합니다.
1. 관리할 계정을 설정하려면 **선택된 클래스, 그룹 또는 개체**를 선택하고 **선택...** 을 클릭한 다음 **그룹...** 을 클릭하여 **그룹 검색** 상자를 엽니다.
1. **Microsoft System Center Advisor 모니터링 서버 그룹**을 검색한 다음 선택합니다. 그룹을 선택한 후 **확인**을 클릭하여 **그룹 검색** 상자를 닫습니다.
1. **확인**을 클릭하여 **실행 계정 추가** 상자를 닫습니다.
1. **저장** 을 클릭하여 마법사를 완료하고 변경 내용을 저장합니다.

연결을 만들고 Azure Monitor에 로그 데이터를 수집하고 보고할 에이전트를 구성한 후 다음 구성이 관리 그룹에 적용됩니다.

* 실행 계정 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** 가 만들어집니다. 이 계정은 실행 프로필 **Microsoft System Center Advisor Run As Profile Blob**과 연결되고 두 개의 클래스 **수집 서버** 및 **Operations Manager 관리 그룹**을 대상으로 합니다.
* 두 개의 커넥터가 생성됩니다.  첫 번째는 **Microsoft.SystemCenter.Advisor.DataConnector로** 명명되며 관리 그룹의 모든 클래스의 인스턴스에서 생성된 모든 경고를 Azure Monitor로 전달하는 구독으로 자동으로 구성됩니다. 두 번째 커넥터는 Azure Monitor와 통신하고 데이터를 공유하는 것을 담당하는 **어드바이저 커넥터입니다.**
* 관리 그룹에서 데이터를 수집하도록 선택한 에이전트 및 그룹은 **Microsoft System Center Advisor 모니터링 서버 그룹**에 추가됩니다.

## <a name="management-pack-updates"></a>관리 팩 업데이트

구성이 완료되면 운영 관리자 관리 그룹은 Azure Monitor와의 연결을 설정합니다. 관리 서버는 웹 서비스와 동기화하고 Operations Manager와 통합하도록 활성화한 솔루션에 대한 관리 팩의 형태로 업데이트된 구성 정보를 수신합니다. Operations Manager는 이러한 관리 팩의 업데이트를 확인하고, 해당 업데이트를 사용할 수 있을 때 자동으로 다운로드하여 가져옵니다. 특히 이 동작을 제어하는 두 개의 규칙이 있습니다.

* **Microsoft.SystemCenter.Advisor.MPUpdate** - 기본 Azure 모니터 관리 팩을 업데이트합니다. 기본적으로 12시간마다 실행됩니다.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 작업 영역에서 활성화된 솔루션 관리 팩을 업데이트합니다. 기본적으로 5분마다 실행됩니다.

이러한 두 규칙을 재정의하여 자동 다운로드를 사용하지 않도록 설정하거나 관리 서버가 Azure Monitor와 동기화되는 빈도에 대한 빈도를 수정하여 새 관리 팩을 사용할 수 있고 다운로드해야 하는지 확인할 수 있습니다. [규칙 또는 모니터를 재정의하는 방법](https://technet.microsoft.com/library/hh212869.aspx) 단계를 따라 초 단위 값으로 **Frequency** 매개 변수를 수정하여 동기화 일정을 변경하거나 **Enabled** 매개 변수를 수정하여 규칙을 비활성화합니다. Operations Manager 관리 그룹 클래스의 모든 개체에 대한 재정의를 대상으로 합니다.

프로덕션 관리 그룹에서 관리 팩 릴리스 제어를 위해 기존 변경 제어 프로세스를 계속 따르려면 규칙을 비활성화하고 업데이트가 허용될 때 특정 시간 동안 활성화하면 됩니다. 사용자 환경에 개발 또는 QA 관리 그룹이 있고 인터넷에 연결되어 있는 경우 Log Analytics 작업 영역으로 해당 관리 그룹을 구성하여 이 시나리오를 지원할 수 있습니다. 이렇게 하면 Azure Monitor 관리 팩의 반복 릴리스를 검토하고 평가한 다음 프로덕션 관리 그룹에 릴리스할 수 있습니다.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Operations Manager 그룹을 새 Log Analytics 작업 영역으로 전환

1. 에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.
1. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **로그 분석을**입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택하고 작업 영역을 만듭니다.  
1. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. Log Analytics를 확장하고 **연결**을 선택합니다.
1. 창 중간의 **Operations Management Suite 다시 구성** 링크를 선택합니다.
1. **Log Analytics 등록 마법사**의 지시를 따라 새 Log Analytics 작업 영역과 연결된 관리자 계정의 이메일 주소 또는 전화 번호와 암호를 입력합니다.

   > [!NOTE]
   > **Operations Management Suite 등록 마법사: 작업 영역 선택** 페이지는 사용 중인 기존 작업 영역을 표시합니다.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Azure 모니터를 통해 운영 관리자 통합 유효성 검사

Azure Monitor to Operations Manager 통합이 성공했는지 확인할 수 있는 몇 가지 방법이 있습니다.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Azure Portal에서 통합을 확인하려면

1. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **로그 분석을**입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다.
1. Log Analytics 작업 영역 목록에서 적용 가능한 작업 영역을 선택합니다.  
1. **고급 설정**, **연결된 원본**, **System Center**를 차례로 선택합니다.
1. System Center Operations Manager 섹션 아래의 테이블에 데이터를 마지막으로 받았을 때 에이전트 및 상태 수와 함께 나열된 관리 그룹의 이름이 표시됩니다.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>운영 콘솔에서 통합을 확인하려면

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. **관리 팩**을 선택하고 **찾기:** 텍스트 상자에 **관리자** 또는 **인텔리전스**를 입력합니다.
1. 활성화한 솔루션에 따라 검색 결과에 나열된 해당 관리 팩을 볼 수 있습니다.  예를 들어 경고 관리 솔루션을 활성화한 경우 관리 팩 Microsoft System Center Advisor 경고 관리가 목록에 나타납니다.
1. **모니터링** 보기에서 **Operations Management Suite\Health State** 보기로 이동합니다.  **관리 서버 상태** 창 아래에서 관리 서버를 선택하고 **상세 보기** 창에서 **인증 서비스 URI** 속성의 값이 Log Analytics 작업 영역 ID와 일치하는지 확인합니다.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Azure 모니터와의 통합 제거

Operations Manager 관리 그룹과 Log Analytics 작업 영역 간의 통합이 더 이상 필요하지 않은 경우 관리 그룹에서 연결 및 구성을 올바르게 제거하는 데 필요한 여러 단계가 있습니다. 다음 절차에서는 관리 그룹의 참조를 삭제하고 Azure Monitor 커넥터를 삭제한 다음 서비스와의 통합을 지원하는 관리 팩을 삭제하여 Log Analytics 작업 영역을 업데이트할 수 있습니다.  

운영 관리자와 통합하는 것을 사용하도록 설정한 솔루션에 대한 관리 팩및 Azure Monitor와의 통합을 지원하는 데 필요한 관리 팩은 관리 그룹에서 쉽게 삭제할 수 없습니다. 이는 일부 Azure Monitor 관리 팩에 다른 관련 관리 팩에 대한 종속성이 있기 때문입니다. 다른 관리 팩에 대해 종속성이 있는 관리 팩을 삭제하려면 TechNet 스크립트 센터에서 [종속성이 있는 관리 팩 제거](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) 스크립트를 다운로드합니다.  

1. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 명령 셸을 엽니다.

    > [!WARNING]
    > 계속 진행하기 전에 이름에 Advisor 또는 IntelligencePack이 포함된 사용자 지정 관리 팩이 없는지 확인합니다. 그렇지 않으면 다음 단계에 관리 그룹에서 이들을 삭제합니다.
    >

1. 명령 셸 프롬프트에서 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 그런 다음 `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 다른 System Center Advisor 관리 팩에 대해 종속성이 있는 나머지 모든 관리 팩을 제거하려면 이전에 TechNet 스크립트 센터에서 다운로드한 *RecursiveRemove.ps1*을 사용합니다.  

    > [!NOTE]
    > PowerShell을 사용하여 Advisor 관리 팩을 제거하는 단계는 Microsoft System Center Advisor 또는 Microsoft System Center Advisor Internal 관리 팩을 자동으로 삭제하지 않습니다.  삭제하지 마십시오.  
    >  

1. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 작업 콘솔을 엽니다.
1. **관리** 아래에서 **관리 팩** 노드를 선택하고 **찾기:** 상자에 **관리자**를 입력합니다. 이 경우에도 다음 관리 팩을 관리 그룹으로 가져올 수 있는지 확인합니다.

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Azure Portal에서 **설정** 타일을 클릭합니다.
1. **연결된 원본**을 선택합니다.
1. System Center Operations Manager 섹션 아래의 표에 작업 영역에서 제거하려는 관리 그룹의 이름이 표시됩니다. **마지막 데이터** 열 아래에서 **제거**를 클릭합니다.  

    > [!NOTE]
    > 14일간 연결된 관리 그룹에서 감지된 활동이 없을 경우 그 후에는 **제거** 링크를 사용할 수 없습니다.  
    >

1. 제거를 계속할지 확인하라는 창이 나타납니다.  **예**를 클릭하여 작업을 계속 진행합니다.

두 커넥터(Microsoft.SystemCenter.Advisor.DataConnector 및 Advisor 커넥터)를 삭제하려면 PowerShell 스크립트를 컴퓨터에 저장하고 다음 예제를 사용하여 실행합니다.

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> 이 스크립트를 실행하는 컴퓨터(관리 서버가 아니라면)에 관리 그룹의 버전에 따라 Operations Manager 명령 셸을 설치해야 합니다.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

나중에 관리 그룹을 Log Analytics 작업 영역에 다시 연결하려는 경우 `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` 관리 팩 파일을 다시 가져와야 합니다. 환경에 배포된 System Center Operations Manager 버전에 따라 다음 위치에서 이 파일을 찾을 수 있습니다.

* System Center 2016 - Operations Manager 이상의 `\ManagementPacks` 폴더 아래에 있는 원본 미디어.
* 관리 그룹에 적용된 최신 업데이트 롤업. Operations Manager 2012의 경우 `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` 소스 폴더는 2012 R2에 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`있습니다.

## <a name="next-steps"></a>다음 단계

기능을 추가하고 데이터를 수집하려면 [솔루션 갤러리에서 Azure Monitor 솔루션 추가를](../../azure-monitor/insights/solutions.md)참조하십시오.
