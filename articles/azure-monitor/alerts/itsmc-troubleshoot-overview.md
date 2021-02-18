---
title: ITSMC의 문제 해결
description: IT 서비스 관리 커넥터에서 일반적인 문제를 해결 하는 방법에 대해 알아봅니다.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 991d37b097587e8e4a524c4fc8bc21e9b6af20b5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616669"
---
# <a name="troubleshoot-problems-in-it-service-management-connector"></a>IT 서비스 관리 커넥터 문제 해결

이 문서에서는 ITSMC (IT 서비스 관리 커넥터)의 일반적인 문제 및 문제를 해결 하는 방법에 대해 설명 합니다.

Azure Monitor는 모니터링 데이터에서 중요 한 조건이 발견 될 때 경고를 사전에 알려 줍니다. 이러한 경고는 시스템 사용자에 게 알리는 문제를 식별 하 고 해결 하는 데 도움이 됩니다.

경고를 수신 하는 방법을 선택할 수 있습니다. 메일, SMS 또는 웹 후크를 선택 하거나 솔루션을 자동화할 수도 있습니다. 

또 다른 방법은 ITSMC를 통해 알려 주는 것입니다. ITSMC는 ServiceNow와 같은 외부 티켓 시스템에 경고를 보내는 옵션을 제공 합니다.

## <a name="use-the-dashboard-to-analyze-incident-and-change-request-data"></a>대시보드를 사용 하 여 인시던트 및 변경 요청 데이터 분석

연결을 설정할 때의 구성에 따라 ITSMC는 최대 120 일의 인시던트 및 변경 요청 데이터를 동기화 할 수 있습니다. 이 데이터에 대 한 로그 레코드 스키마를 가져오려면 [ITSM 제품에서 동기화 된 데이터](./itsmc-synced-data.md) 문서를 참조 하세요.

ITSMC 대시보드를 사용 하 여 인시던트 및 변경 요청 데이터를 시각화할 수 있습니다.

![ITSMC 대시보드를 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

또한 대시보드는 커넥터 상태에 대 한 정보를 제공 합니다. 이 정보를 시작 지점으로 사용 하 여 연결 문제를 분석할 수 있습니다. 자세한 내용은 [대시보드를 사용 하 여 오류 조사](./itsmc-dashboard.md)를 참조 하세요.

## <a name="use-service-map-to-visualize-incidents"></a>서비스 맵를 사용 하 여 인시던트 시각화

서비스 맵에서 영향을 받는 컴퓨터에 대해 동기화 된 인시던트를 시각화할 수도 있습니다.

서비스 맵은 Windows 및 Linux 시스템에서 애플리케이션 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 이를 통해 중요 한 서비스를 제공 하는 상호 연결 된 시스템으로 생각 되는 서버를 볼 수 있습니다. 

서비스 맵는 모든 TCP 연결 아키텍처에서 서버, 프로세스 및 포트 간의 연결을 보여 줍니다. 에이전트를 설치 하는 것 외에는 구성이 필요 하지 않습니다. 자세한 내용은 [서비스 맵 사용](../vm/service-map.md)을 참조 하세요.

서비스 맵를 사용 하는 경우 다음 예와 같이 ITSM (IT Service Management) 솔루션에서 만든 서비스 데스크 항목을 볼 수 있습니다.

![Log Analytics 화면을 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="resolve-problems"></a>문제 해결

다음 섹션에서는 일반적인 증상, 가능한 원인 및 해결 방법을 확인 합니다. 

### <a name="a-connection-to-the-itsm-system-fails-and-you-get-an-error-in-saving-connection-message"></a>ITSM 시스템에 대 한 연결이 실패 하 고 "연결을 저장 하는 동안 오류가 발생 했습니다." 메시지가 표시 됩니다.

**원인**: 원인은 다음 옵션 중 하나일 수 있습니다.

* 자격 증명이 잘못 되었습니다.
* 권한이 부족 합니다.
* 웹 앱이 잘못 배포 되었습니다.

**해결 방법**:

* ServiceNow, Cherwell 및 Provance 연결의 경우:
  * 각 연결에 대 한 사용자 이름, 암호, 클라이언트 ID 및 클라이언트 암호를 올바르게 입력 했는지 확인 합니다.  
  * ServiceNow의 경우 해당 ITSM 제품에 대 한 [충분 한 권한이](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role) 있는지 확인 합니다.

* Service Manager 연결의 경우:  
  * 웹 앱이 성공적으로 배포 되 고 하이브리드 연결이 만들어졌는지 확인 합니다. 온-프레미스 Service Manager 컴퓨터에서 연결이 성공적으로 설정 되었는지 확인 하려면 [하이브리드 연결 만들기에 대 한 설명서](./itsmc-connections-scsm.md#configure-the-hybrid-connection)에 설명 된 대로 웹 앱 URL로 이동 합니다.  

### <a name="duplicate-work-items-are-created"></a>중복 작업 항목이 생성 됩니다.

**원인**: 원인은 다음 두 가지 옵션 중 하나일 수 있습니다.

* 경고에 대 한 ITSM 동작이 두 개 이상 정의 되어 있습니다.
* 경고가 해결 됩니다.

**해결** 방법: 다음 두 가지 해결 방법이 있습니다.

* 경고 당 ITSM 작업 그룹이 하나 있는지 확인 합니다.
* ITSMC는 경고가 해결 될 때 일치 하는 작업 항목의 상태 업데이트를 지원 하지 않습니다. 해결 된 새 작업 항목을 만듭니다.

### <a name="work-items-are-not-created"></a>작업 항목이 생성 되지 않음

**원인**:이 증상의 원인에는 여러 가지가 있을 수 있습니다.

* ServiceNow 쪽에서 코드가 수정 되었습니다.
* 사용 권한이 잘못 구성 되었습니다.
* ServiceNow rate 한도가 너무 크거나 너무 낮습니다.
* 새로 고침 토큰이 만료 되었습니다.
* ITSMC가 삭제 되었습니다.

**해결** 방법: [대시보드](itsmc-dashboard.md) 를 확인 하 고 커넥터 상태 섹션에서 오류를 검토 합니다. 그런 다음 [일반적인 오류 및 해결 방법을](itsmc-dashboard-errors.md)검토 합니다.

### <a name="you-cant-create-an-itsm-action-for-an-action-group"></a>작업 그룹에 대 한 ITSM 작업을 만들 수 없습니다.

**원인**: 새로 만든 itsmc 인스턴스는 아직 초기 동기화를 완료 해야 합니다.

**해결** 방법: [일반적인 오류 및 해결 방법을](itsmc-dashboard-errors.md)검토 합니다.