---
title: ServiceNow 동기화 문제를 수동으로 수정 하는 방법
description: Microsoft Azure에서 경고가 다시 ServiceNow를 호출할 수 있도록 ServiceNow에 대 한 연결을 다시 설정 합니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 9a6e46c0b52c22df0682034deaebd58bbfeb34a7
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210066"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>ITSM 커넥터의 문제 해결

이 문서에서는 ITSM 커넥터의 일반적인 문제와 이러한 문제를 해결 하는 방법에 대해 설명 합니다.

Azure Monitor 경고는 모니터링 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 사전에 알려 줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대 한 자세한 내용은 Microsoft Azure의 경고 개요를 참조 하세요.
고객은 메일, SMS, 웹 후크 또는 솔루션 자동화를 통해 경고에 대 한 알림을 받는 방법을 선택할 수 있습니다. 알림이 제공 되는 또 다른 옵션은 ITSM를 사용 하는 것입니다.
ITSM은 ServiceNow와 같은 외부 티켓 시스템으로 경고를 보내는 옵션을 제공 합니다.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>인시던트 및 변경 요청 데이터 시각화 및 분석

연결을 설정할 때의 구성에 따라 ITSMC는 최대 120 일의 인시던트 및 변경 요청 데이터를 동기화 할 수 있습니다. 이 데이터에 대 한 로그 레코드 스키마는이 문서의 [추가 정보 섹션](./itsmc-synced-data.md) 에 제공 됩니다.

ITSMC 대시보드를 사용 하 여 인시던트 및 변경 요청 데이터를 시각화할 수 있습니다.

![ITSMC 대시보드를 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

또한 대시보드는 연결 문제를 분석 하기 위한 시작 지점으로 사용할 수 있는 커넥터 상태에 대 한 정보를 제공 합니다.

### <a name="error-investigation-using-the-dashboard"></a>대시보드를 사용 하 여 오류 조사

대시보드에서 오류를 보려면 다음 단계를 수행 해야 합니다.

1. **모든 리소스** 에서 **servicedesk (*작업 영역 이름*)** 를 찾습니다.

   ![Azure Portal의 최근 리소스를 보여 주는 스크린샷](media/itsmc-definition/create-new-connection-from-resource.png)

2. 왼쪽 창의 **작업 영역 데이터 원본** 에서 **itsm 연결** 을 선택 합니다.

   ![ITSM 연결 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/add-new-itsm-connection.png)

3. 왼쪽 상자 **IT 서비스 관리 커넥터** **요약** 아래에서 **요약 보기** 를 선택 합니다.

    ![보기 요약을 보여 주는 스크린샷](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 왼쪽 상자의 **요약** 아래에서 그래프를 클릭 **IT 서비스 관리 커넥터** 합니다.

    ![그래프 클릭을 보여 주는 스크린샷](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. 이 대시보드를 사용 하 여 커넥터의 상태와 오류를 검토할 수 있습니다.
    ![커넥터 상태를 보여 주는 스크린샷](media/itsmc-resync-servicenow/connector-dashboard.png)

### <a name="dashboard-elements"></a>대시보드 요소

대시보드는이 커넥터를 사용 하 여 ITSM 도구로 전송 된 경고에 대 한 정보를 포함 합니다.
대시보드는 4 개의 부분으로 분할 됩니다.

1. 만든 작업 항목: 그래프와 아래 표에는 유형별 작업 항목 수가 포함 되어 있습니다. 그래프를 클릭 하거나 표를 클릭 하면 작업 항목에 대 한 자세한 정보를 볼 수 있습니다.
    ![만든 작업 항목을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. 영향을 받는 컴퓨터: 테이블에는 구성 항목을 만든 구성 항목에 대 한 세부 정보가 포함 됩니다.
    테이블에서 행을 클릭 하 여 구성 항목에 대 한 자세한 정보를 얻을 수 있습니다.
    "모두 보기"를 클릭 하 여 모든 목록을 보려면 테이블에 제한 된 수의 행이 포함 되어 있습니다.
    ![영향을 받는 컴퓨터를 표시 하는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. 커넥터 상태: 그래프 및 아래 표에는 커넥터의 상태에 대 한 메시지가 포함 되어 있습니다. 테이블의 행에 대 한 그래프를 클릭 하면 커넥터 상태의 메시지에 대 한 자세한 정보를 볼 수 있습니다.
    "모두 보기"를 클릭 하 여 모든 목록을 보려면 테이블에 제한 된 수의 행이 포함 되어 있습니다.
    ![커넥터 상태를 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. 경고 규칙: 테이블에는 검색 된 경고 규칙 수에 대 한 정보가 포함 되어 있습니다.
    테이블에서 행을 클릭 하면 검색 된 규칙에 대 한 자세한 정보를 볼 수 있습니다.
    "모두 보기"를 클릭 하 여 모든 목록을 보려면 테이블에 제한 된 수의 행이 포함 되어 있습니다.
    ![경고 규칙을 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

### <a name="service-map"></a>서비스 맵

서비스 맵에서 영향을 받는 컴퓨터에 대해 동기화 된 인시던트를 시각화할 수도 있습니다.

서비스 맵은 Windows 및 Linux 시스템에서 애플리케이션 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 이를 통해 중요 한 서비스를 제공 하는 상호 연결 된 시스템으로 생각 되는 서버를 볼 수 있습니다. 서비스 맵는 모든 TCP 연결 아키텍처에서 서버, 프로세스 및 포트 간의 연결을 보여 줍니다. 에이전트를 설치 하는 것 외에는 구성이 필요 하지 않습니다. 자세한 내용은 [서비스 맵 사용](../insights/service-map.md)을 참조 하세요.

서비스 맵를 사용 하는 경우 다음과 같이 ITSM 솔루션에서 만들어진 서비스 데스크 항목을 볼 수 있습니다.

![Log Analytics 화면을 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>ITSM 연결 문제 해결

- 연결에서 ITSM 시스템에 연결 하지 못하고 연결 메시지를 저장 하는 **동안 오류가** 발생 하는 경우 다음 단계를 수행 합니다.
   - ServiceNow, Cherwell 및 Provance 연결의 경우:  
     - 각 연결에 대 한 사용자 이름, 암호, 클라이언트 ID 및 클라이언트 암호를 올바르게 입력 했는지 확인 합니다.  
     - 해당 ITSM 제품에 연결을 설정 하는 데 충분 한 권한이 있는지 확인 합니다.  
   - Service Manager 연결의 경우:  
     - 웹 앱이 성공적으로 배포 되 고 하이브리드 연결이 만들어졌는지 확인 합니다. 온-프레미스 Service Manager 컴퓨터에서 연결이 성공적으로 설정 되었는지 확인 하려면 [하이브리드 연결](./itsmc-connections-scsm.md#configure-the-hybrid-connection)에 대 한 설명서에 설명 된 대로 웹 앱 URL로 이동 합니다.  

- ServiceNow의 데이터가 Log Analytics와 동기화 되지 않으면 ServiceNow 인스턴스가 절전 모드에 있지 않은지 확인 합니다. ServiceNow dev 인스턴스는 오랜 시간 동안 유휴 상태일 때 절전 모드로 전환 되는 경우가 있습니다. 문제가 발생 하지 않는 경우 문제를 보고 합니다.
- Log Analytics 경고가 발생 했지만 작업 항목이 ITSM 제품에 생성 되지 않은 경우, 구성 항목이 작업 항목에 생성/연결 되지 않거나 기타 정보에 대 한 자세한 내용은 다음 리소스를 참조 하세요.
   -  ITSMC: 솔루션은 연결, 작업 항목, 컴퓨터 등에 대 한 요약을 보여 줍니다. **커넥터 상태** 레이블이 있는 타일을 선택 합니다. 이렇게 하면 관련 쿼리를 사용 하 여 **로그 검색** 으로 이동 합니다. 자세한 내용은의를 사용 하 여 로그 레코드를 살펴보세요 `LogType_S` `ERROR` .
   - **로그 검색** 페이지: 쿼리를 사용 하 여 오류 및 관련 정보를 직접 확인 합니다 `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>웹 앱 배포 Service Manager 문제 해결

-   웹 앱 배포에 문제가 있는 경우 구독에서 리소스를 만들거나 배포할 수 있는 권한이 있는지 확인 합니다.
-   [스크립트](itsmc-service-manager-script.md)를 실행할 때 개체 **참조를 개체의 인스턴스로 설정 하지** 않으면 **사용자 구성** 섹션에서 올바른 값을 입력 했는지 확인 합니다.
-   Service bus relay 네임 스페이스를 만들지 못한 경우 필요한 리소스 공급자가 구독에 등록 되어 있는지 확인 합니다. 등록 되지 않은 경우 Azure Portal에서 service bus relay 네임 스페이스를 수동으로 만듭니다. Azure Portal에서 [하이브리드 연결을 만들](./itsmc-connections-scsm.md#configure-the-hybrid-connection) 때도 만들 수 있습니다.

### <a name="how-to-manually-fix-sync-problems"></a>동기화 문제를 수동으로 해결 하는 방법

Azure Monitor 타사 ITSM (IT Service Management) 공급자에 연결할 수 있습니다. ServiceNow는 이러한 공급자 중 하나입니다.

보안상의 이유로 ServiceNow를 사용 하 여 연결 하는 데 사용 되는 인증 토큰을 새로 고쳐야 할 수도 있습니다.
다음 동기화 프로세스를 사용 하 여 연결을 다시 활성화 하 고 토큰을 새로 고칩니다.


1. 위쪽 검색 배너에서 솔루션을 검색 한 다음 관련 솔루션을 선택 합니다.

    ![위쪽 검색 배너와 관련 솔루션을 선택할 수 있는 위치를 보여 주는 스크린샷](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 솔루션 화면에서 구독 필터의 "모두 선택"을 선택한 다음 "ServiceDesk"로 필터링 합니다.

    ![모두 선택을 선택할 수 있는 위치와 ServiceDesk를 기준으로 필터링 할 위치를 보여 주는 스크린샷](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. ITSM 연결의 솔루션을 선택 합니다.
1. 왼쪽 배너에서 ITSM 연결을 선택 합니다.

    ![ITSM 연결을 선택할 수 있는 위치를 보여 주는 스크린샷](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 목록에서 각 커넥터를 선택 합니다. 
    1. 커넥터 이름을 클릭 하 여 구성 합니다.
    1. 더 이상 사용 하지 않는 모든 커넥터 삭제

    1. 파트너 유형에 따라 [이러한 정의](./itsmc-connections.md) 에 따라 필드를 업데이트 합니다.

    1. 동기화를 클릭 합니다.

       ![동기화 단추를 강조 표시 하는 스크린샷](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 저장을 클릭 합니다.

        ![새 연결](media/itsmc-resync-servicenow/save-8bit.png)

f.    알림을 검토 하 여 프로세스가 시작 되는지 확인 합니다.
