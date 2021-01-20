---
title: ITSM 커넥터의 문제 해결
description: IT 서비스 관리 커넥터 문제 해결
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2ffe7c8994d32917a08896c7d25f20d4adf09066
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601898"
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

대시보드 조사에 대 한 자세한 정보를 보려면 [대시보드를 사용 하 여 오류 조사](./itsmc-dashboard.md)를 참조 하세요.

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

- Log Analytics 경고가 발생 했지만 작업 항목이 ITSM 제품에 생성 되지 않은 경우, 구성 항목이 작업 항목에 생성/연결 되지 않거나 기타 정보에 대 한 자세한 내용은 다음 리소스를 참조 하세요.
   -  ITSMC: 솔루션은 연결, 작업 항목, 컴퓨터 등에 [대 한 요약](itsmc-dashboard.md)을 보여 줍니다. **커넥터 상태** 레이블이 있는 타일을 선택 합니다. 이렇게 하면 관련 쿼리를 사용 하 여 **로그 검색** 으로 이동 합니다. 자세한 내용은의를 사용 하 여 로그 레코드를 살펴보세요 `LogType_S` `ERROR` .
   테이블에서 메시지에 대 한 세부 정보를 볼 수 [있습니다.](itsmc-dashboard-errors.md)
   - **로그 검색** 페이지: 쿼리를 사용 하 여 오류 및 관련 정보를 직접 확인 합니다 `*ServiceDeskLog_CL*` .

## <a name="common-symptoms---how-it-should-be-resolved"></a>일반적인 증상-어떻게 해결 해야 하나요?

아래 목록에는 일반적인 증상과이를 해결 하는 방법이 포함 되어 있습니다.

* **증상**: 중복 작업 항목이 생성 됨

    **원인**: 원인은 다음 두 가지 옵션 중 하나일 수 있습니다.
    * 경고에 대 한 ITSM 동작이 두 개 이상 정의 되어 있습니다.
    * 경고가 해결 되었습니다.

    **해결** 방법: 다음 두 가지 해결 방법이 있습니다.
    * 경고 당 ITSM 작업 그룹이 하나 있는지 확인 합니다.
    * ITSM 커넥터는 경고가 해결 될 때 일치 하는 작업 항목 상태 업데이트를 지원 하지 않습니다. 해결 된 새 작업 항목이 생성 됩니다.
* **증상**: 작업 항목이 생성 되지 않습니다.

    **원인**:이 증상에는 몇 가지 이유가 있을 수 있습니다.
    * ServiceNow 쪽에서 코드 수정
    * 사용 권한 잘못 구성
    * ServiceNow rate 한도가 너무 높음/낮음
    * 새로 고침 토큰이 만료 되었습니다.
    * ITSM 커넥터 삭제 됨

    **해결** 방법: [대시보드](itsmc-dashboard.md) 를 확인 하 고 커넥터 상태 섹션에서 오류를 검토할 수 있습니다. [일반적인 오류](itsmc-dashboard-errors.md) 를 검토 하 고 오류를 해결 하는 방법을 확인 합니다.

* **증상**: 작업 그룹에 대 한 Itsm 작업을 만들 수 없습니다.

    **원인**: 새로 만든 ITSM 커넥터에서 아직 초기 동기화를 완료 했습니다.

    **해결** 방법: [일반적인 UI 오류](itsmc-dashboard-errors.md#ui-common-errors) 를 검토 하 고 오류를 해결 하는 방법을 확인할 수 있습니다.