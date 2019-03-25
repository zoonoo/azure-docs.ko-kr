---
title: Azure Monitor에 Operations Manager 연결 | Microsoft Docs
description: System Center Operations Manager의 기존 투자를 유지 관리하고 Log Analytics로 확장된 기능을 사용하려면 작업 영역으로 Operations Manager를 통합할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/23/2018
ms.author: magoedte
ms.openlocfilehash: 3e11c4dc8aa082723223fb998e599aa514febd3b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369969"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Azure Monitor에 Operations Manager 연결

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

에 대 한 기존 투자를 유지 하기 위해 [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) 및 확장 된 기능을 사용 하 여 Azure monitor, Log Analytics 작업 영역을 사용 하 여 Operations Manager를 통합할 수 있습니다. 이렇게 하면 Operations Manager를 사용 하면서 Azure Monitor의 로그의 기회를 활용 합니다.

* Operations Manager를 사용하여 IT 서비스의 상태 모니터링
* 인시던트 및 문제 관리를 지원하는 ITSM 솔루션과 통합 유지 관리
* 온-프레미스 및 Operations Manager로 모니터링하는 공용 클라우드 IaaS 가상 머신에 배포된 에이전트의 수명 주기 관리

속도 및 효율성의 수집, 저장 및 Operations Manager에서 로그 데이터를 분석에 대 한 Azure Monitor를 사용 하 여 서비스 작업 전략에 값을 추가 System Center Operations Manager와 통합 합니다. Azure 로그의 모니터링 도움말 상호 연결 및 작업 오류 문제를 식별 하 고 기존 문제 관리 프로세스를 지원 하기 위해 재발을 쿼리 합니다. Operations Manager 강점을 성능, 이벤트 및 다양 한 대시보드 및이 데이터를 의미 있는 방식으로 노출 하는 보고 기능을 사용 하 여 데이터를 보여 줍니다 Azure Monitor 경고를 검사 하는 쿼리 엔진의 유연성은 제공 합니다.

Operations Manager 관리 그룹에 보고 하는 에이전트에 기반한 서버에서 데이터를 수집 합니다 [Log Analytics 데이터 원본](agent-data-sources.md) 및 작업 영역에서 활성화 한 솔루션입니다. 활성화 한 솔루션에 따라, 해당 데이터는 전송 되거나 Operations Manager 관리 서버에서 직접 서비스 또는 에이전트 관리 시스템에서 수집 된 데이터의 양으로 인해 Log Analytics 작업 영역에 에이전트에서 직접 전송 됩니다. 관리 서버는 데이터를 서비스에 직접 전달하며, 운영 또는 데이터 웨어하우스 데이터베이스에 기록되지 않습니다. 관리 서버를 Azure Monitor를 사용 하 여 연결을 손실 하는 경우 캐시 데이터를 로컬로 통신 다시 설정 될 때까지. 관리 서버가 계획 된 유지 관리 또는 계획 되지 않은 중단으로 인해 오프 라인 상태인 경우 다른 관리 서버 관리 그룹에서 Azure Monitor를 사용 하 여 연결을 다시 시작 합니다.  

다음 다이어그램은 방향 및 포트를 포함 하 여 Azure Monitor를 System Center Operations Manager 관리 그룹에 관리 서버와 에이전트 간 연결을 보여줍니다.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

IT 보안 정책이 네트워크의 컴퓨터가 인터넷에 연결하도록 허용하지 않을 경우 Log Analytics 게이트웨이에 연결하여 구성 정보를 받고 사용하도록 설정한 솔루션에 따라 수집된 데이터를 보내도록 관리 서버를 구성할 수 있습니다. 자세한 내용 및 Log Analytics 게이트웨이 Azure Monitor를 통해 통신 하도록 Operations Manager 관리 그룹을 구성 하는 방법에 대 한 단계를 참조 하세요 [Log Analytics 게이트웨이 사용 하 여 Azure Monitor에 컴퓨터 연결](../../azure-monitor/platform/gateway.md)합니다.  

## <a name="prerequisites"></a>필수 조건 
시작에 앞서 다음 요구 사항을 검토합니다.

* Azure Monitor 지원 System Center Operations Manager 1807, Operations Manager 1801, Operations Manager 2016, Operations Manager 2012 SP1 UR6 이상 및 Operations Manager 2012 R2 UR2 이상. 프록시 지원은 Operations Manager 2012 SP1 UR7 및 Operations Manager 2012 R2 UR3에 추가되었습니다.
* 모든 Operations Manager 에이전트는 최소 지원 요구 사항을 만족해야 합니다. 에이전트가 최소 업데이트를 따르고 있는지 확인하고, 그렇지 않으면 Windows 에이전트 통신이 실패하고 Operations Manager 이벤트 로그에 오류가 생성될 수 있습니다.
* Log Analytics 작업 영역. 자세한 내용은 [Log Analytics 작업 영역 개요](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json)를 검토합니다.
* [Log Analytics 기여자 역할](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users)의 멤버인 계정으로 Azure에 인증합니다.  

>[!NOTE]
>Azure Api에 최근 변경 내용을 성공적으로 처음에 대 한 Azure Monitor와 관리 그룹 간의 통합을 구성 하려면 못하도록 고객 수 없게 됩니다. 관리 그룹과 서비스를 이미 통합한 고객의 경우, 기존 연결을 재구성해야 하는 경우가 아니면 영향을 받지 않습니다.  
>다음 버전의 Operations Manager에 대해 새로운 관리 팩이 릴리스되었습니다.
>  
>* System Center Operations Manager 1801은 [여기](https://www.microsoft.com/download/details.aspx?id=57173)에서 관리 팩을 다운로드받으세요.  
>* System Center 2016 - Operations Manager는 [여기](https://www.microsoft.com/download/details.aspx?id=57172)에서 관리 팩을 다운로드받으세요.  
>* System Center Operations Manager 2012 R2는 [여기](https://www.microsoft.com/download/details.aspx?id=57171)에서 관리 팩을 다운로드받으세요.  
>
>이 관리 팩 업데이트는 System Center Operations Manager 1807에 적용할 수 없습니다. 이 버전은 1801 버전의 업데이트 릴리스이며 제품 전체 빌드가 아니기 때문입니다.   

### <a name="network"></a>네트워크
Azure Monitor를 사용 하 여 통신 하도록 Operations Manager 에이전트, 관리 서버 및 운영 콘솔에 필요한 프록시 및 방화벽 구성 정보를 나열 하는 아래 정보입니다. 각 구성 요소의 트래픽은 네트워크에서 Azure Monitor 아웃 바운드 됩니다.   

|리소스 | 포트 번호| HTTP 검사 무시|  
|---------|------|-----------------------|  
|**에이전트**|||  
|\*.ods.opinsights.azure.com| 443 |예|  
|\*.oms.opinsights.azure.com| 443|예|  
|\*.blob.core.windows.net| 443|예|  
|\*.azure-automation.net| 443|예|  
|**관리 서버**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| 예|  
|\*.ods.opinsights.azure.com| 443| 예|  
|*.azure-automation.net | 443| 예|  
|**Azure Monitor에 operations Manager 콘솔**|||  
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
Azure Monitor로 전송 되에서는 데이터의 보안을 위해 가장 좋습니다 이상을 사용 하려면 에이전트와 관리 그룹을 구성 하려면 전송 계층 보안 (TLS) 1.2입니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)은 취약한 것으로 나타났으며, 여전히 이전 버전과 호환되지만 **사용하지 않는 것이 좋습니다**. 자세한 내용은 [TLS 1.2를 사용하여 안전하게 데이터 보내기](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)를 검토하세요. 

## <a name="connecting-operations-manager-to-azure-monitor"></a>Azure Monitor에 Operations Manager 연결
Operations Manager 관리 그룹을 구성하도록 다음과 같은 일련의 단계를 수행하여 Log Analytics 작업 영역 중 하나에 연결합니다.

Log Analytics 작업 영역에 Operations Manager 관리 그룹을 처음으로 등록하는 동안 관리 그룹에 대한 프록시 구성을 지정하는 옵션을 운영 콘솔에서 사용할 수 없습니다.  이 옵션을 사용하려면 관리 그룹이 서비스에 등록되어 있어야 합니다.  이 문제를 해결하려면 운영 콘솔을 실행하는 시스템에서 Netsh를 사용하여 관리 그룹의 통합 및 모든 관리 서버를 구성하여 시스템 프록시 구성을 업데이트해야 합니다.  

1. 관리자 권한 명령 프롬프트를 엽니다.
   a. **시작**으로 이동하여 **cmd**를 입력합니다.
   b. **명령 프롬프트**를 마우스 오른쪽 단추로 클릭하고 관리자 권한으로 실행**을 선택합니다.
1. 다음 명령을 입력하고 **Enter** 키를 누릅니다.

    `netsh winhttp set proxy <proxy>:<port>`

Azure Monitor와 통합 하려면 다음 단계를 완료 한 후 구성을 실행 하 여 제거할 수 있습니다 `netsh winhttp reset proxy` 한 다음 사용 합니다 **프록시 서버 구성** 프록시 또는 로그를 지정 하려면 운영 콘솔의 옵션 게이트웨이 서버를 분석 합니다. 

1. Operations Manager 콘솔에서 **관리** 작업 영역을 선택합니다.
1. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
1. **Operations Management Suite에 등록** 링크를 클릭합니다.
1. **Operations Management Suite 온보딩 마법사: 인증** 페이지에서 OMS 구독과 연결된 관리자 계정의 메일 주소 또는 전화 번호와 암호를 입력하고 **로그인**을 클릭합니다.

   >[!NOTE]
   >Operations Management Suite 이름은 사용이 중지 되었습니다. 
   
1. 성공적으로 인증되면 **Operations Management Suite 온보딩 마법사: 작업 영역 선택** 페이지에 Azure 테넌트, 구독 및 Log Analytics 작업 영역을 선택하라는 메시지가 표시됩니다. 둘 이상의 작업 영역이 있는 경우 드롭다운 목록에서 Operations Manager 관리 그룹으로 등록하려는 작업 영역을 선택한 후 **다음**을 클릭합니다.
   
   > [!NOTE]
   > Operations Manager는 한 번에 하나의 Log Analytics 작업 영역을 지원합니다. 연결 및 이전 작업 영역을 사용 하 여 Azure Monitor에 등록 된 컴퓨터는 Azure Monitor에서 제거 됩니다.
   > 
   > 
1. **Operations Management Suite 온보딩 마법사: 요약** 페이지에서 설정을 확인하고, 올바른 경우 **만들기**를 클릭합니다.
1. **Operations Management Suite 온보딩 마법사: 완료** 페이지에서 **닫기**를 클릭합니다.

### <a name="add-agent-managed-computers"></a>에이전트 관리 컴퓨터 추가
Log Analytics 작업 영역과 통합을 구성한 후 Log Analytics와의 연결을 설정하고 에이전트 보고에서 관리 그룹으로 데이터가 수집되지 않습니다. 이 값은 Azure Monitor에 대 한 로그 데이터를 수집 하는 특정 에이전트 관리 컴퓨터를 구성한 후 될 때까지 일어나지 않습니다. 컴퓨터 개체를 개별적으로 선택하거나 Windows 컴퓨터 개체를 포함하는 그룹을 선택할 수 있습니다. 논리 디스크 또는 SQL 데이터베이스와 같은 다른 클래스의 인스턴스를 포함하는 그룹을 선택할 수 없습니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. Operations Management Suite 노드를 확장하고 **연결**을 클릭합니다.
1. 창 오른쪽 작업 제목 아래의 **컴퓨터/그룹 추가** 링크를 클릭합니다.
1. **컴퓨터 검색** 대화 상자에서, Operations Manager에서 모니터링하는 컴퓨터 또는 그룹을 검색할 수 있습니다. 컴퓨터 또는 Azure Monitor에 등록 하는 그룹 선택, 클릭 **추가**를 클릭 하 고 **확인**합니다.

운영 콘솔의 **관리** 작업 영역에서 Operations Management Suite 아래의 관리되는 컴퓨터 노드에서 데이터를 수집하도록 구성된 컴퓨터 및 그룹을 볼 수 있습니다. 여기에서 필요에 따라 컴퓨터 및 그룹을 추가하거나 제거할 수 있습니다.

### <a name="configure-proxy-settings-in-the-operations-console"></a>운영 콘솔에서 프록시 설정 구성
내부 프록시 서버를 관리 그룹 및 Azure Monitor 사이인 경우 다음 단계를 수행 합니다. 중앙 집중식으로 이러한 설정은 관리 그룹에서 관리 되며 Azure Monitor에 대 한 로그 데이터를 수집 하는 범위에 포함 된 에이전트 관리 시스템에 배포 합니다.  이는 특정 솔루션이 관리 서버를 우회하고 데이터를 Log Analytics 서비스에 직접 보낼 때 효과적입니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. Operations Management Suite를 확장한 다음 **연결**을 클릭합니다.
1. OMS 연결 보기에서 **프록시 서버 구성**을 클릭합니다.
1. **Operations Management Suite 마법사: 프록시 서버** 페이지에서 **프록시 서버를 사용하여 Operations Management Suite에 액세스**를 선택하고 포트 번호와 함께 URL을 입력(예: http://corpproxy:80)한 다음, **마침**을 클릭합니다.

프록시 서버에 인증이 필요한 경우 자격 증명 및 Azure Monitor에 보고 하는 관리 그룹에는 관리 되는 컴퓨터에 전파 해야 하는 설정을 구성 하려면 다음 단계를 수행 합니다.

1. Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. **RunAs 구성**에서 **프로필**을 선택합니다.
1. **프로필 프록시로 시스템 센터 관리자 실행** 프로필을 엽니다.
1. 실행 프로필 마법사에서 추가를 클릭하여 실행 계정을 사용합니다. [실행 계정](https://technet.microsoft.com/library/hh321655.aspx)을 만들거나 기존 계정을 사용할 수 있습니다. 이 계정에는 프록시 서버를 통과할 수 있는 권한이 있어야 합니다.
1. 관리할 계정을 설정하려면 **선택된 클래스, 그룹 또는 개체**를 선택하고 **선택...** 을 클릭한 다음 **그룹...** 을 클릭하여 **그룹 검색** 상자를 엽니다.
1. **Microsoft System Center Advisor 모니터링 서버 그룹**을 검색한 다음 선택합니다. 그룹을 선택한 후 **확인**을 클릭하여 **그룹 검색** 상자를 닫습니다.
1. **확인**을 클릭하여 **실행 계정 추가** 상자를 닫습니다.
1. **저장** 을 클릭하여 마법사를 완료하고 변경 내용을 저장합니다.

연결이 생성 된 후 에이전트는 수집 하 고 Azure Monitor에 로그 데이터를 보고를 구성한 다음 구성의 관리 그룹 순서에 관계 없이 적용 됩니다.

* 실행 계정 **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** 가 만들어집니다. 이 계정은 실행 프로필 **Microsoft System Center Advisor Run As Profile Blob**과 연결되고 두 개의 클래스 **수집 서버** 및 **Operations Manager 관리 그룹**을 대상으로 합니다.
* 두 개의 커넥터가 생성됩니다.  첫 번째 이름은 **Microsoft.SystemCenter.Advisor.DataConnector** Azure Monitor에 관리 그룹의 모든 클래스의 인스턴스에서 생성 된 모든 경고를 전달 하는 구독을 사용 하 여 자동으로 구성 됩니다. 두 번째 커넥터는 **Advisor 커넥터**를 담당 하는 Azure Monitor를 사용 하 여 통신 및 데이터를 공유 합니다.
* 관리 그룹에서 데이터를 수집하도록 선택한 에이전트 및 그룹은 **Microsoft System Center Advisor 모니터링 서버 그룹**에 추가됩니다.

## <a name="management-pack-updates"></a>관리 팩 업데이트
구성이 완료 되 면 Operations Manager 관리 그룹은 Azure Monitor를 사용 하 여 연결을 설정 합니다. 관리 서버는 웹 서비스와 동기화하고 Operations Manager와 통합하도록 활성화한 솔루션에 대한 관리 팩의 형태로 업데이트된 구성 정보를 수신합니다. Operations Manager는 이러한 관리 팩의 업데이트를 확인하고, 해당 업데이트를 사용할 수 있을 때 자동으로 다운로드하여 가져옵니다. 특히 이 동작을 제어하는 두 개의 규칙이 있습니다.

* **Microsoft.SystemCenter.Advisor.MPUpdate** -기본 Azure Monitor 관리 팩을 업데이트 합니다. 기본적으로 12시간마다 실행됩니다.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - 작업 영역에서 활성화된 솔루션 관리 팩을 업데이트합니다. 기본적으로 5분마다 실행됩니다.

재정의할 수 있습니다 이러한 두 규칙을 비활성화 하 여 자동 다운로드를 방지 하거나 Azure Monitorto와 관리 서버를 동기화 하는 빈도 대 한 빈도 수정 하는 경우 새 관리 팩을 사용할 수 있고이 다운로드 해야 하는 확인 합니다. [규칙 또는 모니터를 재정의하는 방법](https://technet.microsoft.com/library/hh212869.aspx) 단계를 따라 초 단위 값으로 **Frequency** 매개 변수를 수정하여 동기화 일정을 변경하거나 **Enabled** 매개 변수를 수정하여 규칙을 비활성화합니다. Operations Manager 관리 그룹 클래스의 모든 개체에 대한 재정의를 대상으로 합니다.

프로덕션 관리 그룹에서 관리 팩 릴리스 제어를 위해 기존 변경 제어 프로세스를 계속 따르려면 규칙을 비활성화하고 업데이트가 허용될 때 특정 시간 동안 활성화하면 됩니다. 사용자 환경에 개발 또는 QA 관리 그룹이 있고 인터넷에 연결되어 있는 경우 Log Analytics 작업 영역으로 해당 관리 그룹을 구성하여 이 시나리오를 지원할 수 있습니다. 이 검토 및 Azure Monitor 관리 팩의 반복적인 릴리스를 프로덕션 관리 그룹으로 릴리스하기 전에 평가할 수 있습니다.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Operations Manager 그룹을 새 Log Analytics 작업 영역으로 전환
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
1. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택하고 작업 영역을 만듭니다.  
1. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 콘솔을 열고 **관리** 작업 영역을 선택합니다.
1. Log Analytics를 확장하고 **연결**을 선택합니다.
1. 창 중간의 **Operations Management Suite 다시 구성** 링크를 선택합니다.
1. **Log Analytics 등록 마법사**의 지시를 따라 새 Log Analytics 작업 영역과 연결된 관리자 계정의 이메일 주소 또는 전화 번호와 암호를 입력합니다.
   
   > [!NOTE]
   > **Operations Management Suite 온보딩 마법사: 작업 영역 선택** 페이지에 사용 중인 기존 작업 영역이 표시됩니다.
   > 
   > 

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Azure Monitor 사용 하 여 Operations Manager 통합 유효성 검사
몇 가지 다른 방법이 있습니다 Azure Monitor에서 Operations Manager 통합이 성공적인 지 확인할 수 있습니다.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Azure Portal에서 통합을 확인하려면
1. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다.
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

## <a name="remove-integration-with-azure-monitor"></a>Azure Monitor와의 통합 제거
Operations Manager 관리 그룹과 Log Analytics 작업 영역 간의 통합이 더 이상 필요하지 않은 경우 관리 그룹에서 연결 및 구성을 올바르게 제거하는 데 필요한 여러 단계가 있습니다. 다음 절차에서는 관리 그룹에 대 한 참조를 삭제 하 여 Log Analytics 작업 영역을 업데이트 하 고 Azure Monitor 커넥터를 삭제 한 다음 서비스와의 통합을 지 원하는 관리 팩을 삭제 하 고 있습니다.  

솔루션에 대 한 관리 팩 활성화 한 Operations Manager와 통합 되는 및 Azure Monitor와 통합을 지 원하는 데 필요한 관리 팩은 관리 그룹에서 쉽게 삭제할 수 없습니다. 다른 관련된 관리 팩에 대해 종속성이 있는 Azure Monitor 관리 팩의 일부 때문입니다. 다른 관리 팩에 대해 종속성이 있는 관리 팩을 삭제하려면 TechNet 스크립트 센터에서 [종속성이 있는 관리 팩 제거](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) 스크립트를 다운로드합니다.  

1. Operations Manager 관리자 역할의 구성원인 계정을 사용하여 Operations Manager 명령 셸을 엽니다.
   
    > [!WARNING]
    > 계속 진행하기 전에 이름에 Advisor 또는 IntelligencePack이 포함된 사용자 지정 관리 팩이 없는지 확인합니다. 그렇지 않으면 다음 단계에 관리 그룹에서 이들을 삭제합니다.
    > 

1. 명령 셸 프롬프트에서 `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. 그런 다음 `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
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

1. 제거를 계속할지 확인하라는 창이 나타납니다.  **예** 를 클릭하여 계속 진행합니다. 

두 커넥터(Microsoft.SystemCenter.Advisor.DataConnector 및 Advisor 커넥터)를 삭제하려면 PowerShell 스크립트를 컴퓨터에 저장하고 다음 예제를 사용하여 실행합니다.

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> 이 스크립트를 실행하는 컴퓨터(관리 서버가 아니라면)에 관리 그룹의 버전에 따라 Operations Manager 명령 셸을 설치해야 합니다.
> 
> 

```
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
* 관리 그룹에 적용된 최신 업데이트 롤업. Operations Manager 2012의 경우 원본 폴더는 ` %ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups`이고 2012 R2의 경우 `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`에 있습니다.

## <a name="next-steps"></a>다음 단계
기능을 추가 하 고 데이터를 수집, 참조 [솔루션 갤러리에서 Azure Monitor 추가 솔루션](../../azure-monitor/insights/solutions.md)합니다.


