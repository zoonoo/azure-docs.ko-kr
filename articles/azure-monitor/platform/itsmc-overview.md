---
title: Log Analytics IT 서비스 관리 커넥터
description: 이 문서에서는 ITSMC (IT 서비스 관리 커넥터)의 개요와이를 사용 하 여 Log Analytics에서 ITSMC 작업 항목을 모니터링 및 관리 하 고 문제를 빠르게 해결 하는 방법에 대 한 정보를 제공 합니다.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: ecafc0c81a6614a914d8cad3d2c35fd04544b8f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102023"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>IT 서비스 관리 커넥터를 사용 하 여 ITSM 도구에 Azure 연결

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

ITSMC (IT 서비스 관리 커넥터)를 사용 하면 지원 되는 ITSMC (IT Service Management) 제품 또는 서비스에 Azure를 연결할 수 있습니다.

Azure Log Analytics 및 Azure Monitor와 같은 azure 서비스는 Azure 및 비 Azure 리소스의 문제를 검색, 분석 및 해결 하는 도구를 제공 합니다. 하지만 문제와 관련 된 작업 항목은 일반적으로 ITSM 제품 또는 서비스에 있습니다. ITSMC는 Azure와 ITSMC 도구 간에 양방향 연결을 제공 하 여 문제를 더 빠르게 해결할 수 있도록 합니다.

ITSMC는 다음 ITSM 도구와의 연결을 지원합니다.

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

ITSMC를 사용하면 다음 작업을 수행할 수 있습니다.

-  Azure 경고 (메트릭 경고, 활동 로그 경고 및 Log Analytics 경고)에 따라 ITSM 도구에서 작업 항목을 만듭니다.
-  필요에 따라 인시던트 및 변경 요청 데이터를 ITSM 도구에서 Azure Log Analytics 작업 영역으로 동기화할 수 있습니다.

약관 및 개인 정보 취급 방침에 대 한 자세한 내용은 [Microsoft 개인 정보 취급](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)방침을 참조 하세요.

다음 단계를 완료 하 여 ITSMC 사용을 시작할 수 있습니다.

1.  [ITSMC를 추가 합니다.](#add-it-service-management-connector)
2.  [ITSM 연결을 만듭니다.](#create-an-itsm-connection)
3.  [연결을 사용 합니다.](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>IT 서비스 관리 커넥터 추가

연결을 만들려면 ITSMC를 추가 해야 합니다.

1. Azure Portal에서 **리소스 만들기** 를 선택 합니다.

   ![리소스 만들기 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/azure-add-new-resource.png)

2. Azure Marketplace에서 **IT 서비스 관리 커넥터** 를 검색 합니다. **만들기** 를 선택 합니다.

   ![Azure Marketplace에서 만들기 단추를 보여 주는 스크린샷](media/itsmc-overview/add-itsmc-solution.png)

3. **OMS 작업 영역** 섹션에서 itsmc를 설치 하려는 Azure Log Analytics 작업 영역을 선택 합니다.
   >[!NOTE]
   > * Microsoft Operations Management Suite (OMS)에서 Azure Monitor로 진행 중인 전환의 일부로, 이제 OMS 작업 영역을 *Log Analytics 작업 영역* 이라고 합니다.
   > * ITSMC는 미국 동부, 미국 서 부 2, 미국 중부, 미국 서 부, US Gov 애리조나, US Gov 버지니아, 캐나다 중부, 유럽 서부, 남부 영국, 동남 아시아, 일본 동부, 인도 중부 및 오스트레일리아 남동쪽 지역의 Log Analytics 작업 영역에만 설치할 수 있습니다.


4. **Log Analytics 작업 영역** 섹션에서 itsmc 리소스를 만들려는 리소스 그룹을 선택 합니다.

   ![Log Analytics 작업 영역 섹션을 보여 주는 스크린샷](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Microsoft Operations Management Suite (OMS)에서 Azure Monitor로 진행 중인 전환의 일부로, 이제 OMS 작업 영역을 *Log Analytics 작업 영역* 이라고 합니다.

5. **확인** 을 선택합니다.

ITSMC 리소스를 배포 하면 창의 오른쪽 위 모퉁이에 알림이 표시 됩니다.


## <a name="create-an-itsm-connection"></a>ITSM 연결 만들기

ITSMC를 설치한 후에는 연결을 만들 수 있습니다.

연결을 만들려면 ITSM에서 연결을 허용 하도록 ITSM 도구를 준비 해야 합니다.  

연결 하려는 ITSM 제품을 기반으로 다음 링크 중 하나를 선택 합니다.

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

ITSM 도구를 준비 후 다음 단계를 수행 하 여 연결을 만듭니다.

1. **모든 리소스** 에서 **servicedesk ( *작업 영역 이름* )** 를 찾습니다.

   ![Azure Portal의 최근 리소스를 보여 주는 스크린샷](media/itsmc-overview/itsm-connections.png)

1. 왼쪽 창의 **작업 영역 데이터 원본** 에서 **itsm 연결** 을 선택 합니다.

   ![ITSM 연결 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/add-new-itsm-connection.png)
   이 페이지에는 연결 목록이 표시됩니다.
1. **연결 추가** 를 선택 합니다.

4. [Itsmc 제품/서비스를 사용 하 여 ITSMC 연결 구성](./itsmc-connections.md)에 설명 된 대로 연결 설정을 지정 합니다.

   > [!NOTE]
   >
   > 기본적으로 ITSMC는 24 시간 마다 한 번씩 연결의 구성 데이터를 새로 고칩니다. 사용자가 만든 편집 내용 또는 템플릿 업데이트를 반영 하도록 연결 데이터를 즉시 새로 고치려면 연결의 블레이드에서 **동기화** 단추를 선택 합니다.
   >
   > ![연결 블레이드의 동기화 단추를 보여 주는 스크린샷](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>ITSMC 사용
   ITSMC를 사용 하 여 Azure 경고에서 작업 항목을 만들고, 경고를 Log Analytics 하 고, 로그 레코드를 Log Analytics 수 있습니다.

## <a name="template-definitions"></a>템플릿 정의
   ITSM 도구에 정의 된 템플릿을 사용할 수 있는 작업 항목 형식이 있습니다.
템플릿을 사용 하면 작업 그룹의 일부로 정의 된 고정 값에 따라 자동으로 채워지는 필드를 정의할 수 있습니다. ITSM 도구에서 템플릿을 정의 합니다.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure Alerts에서 ITSM 작업 항목 만들기

ITSM 연결을 만든 후에는 Azure 경고를 기반으로 ITSM 도구에서 작업 항목을 만들 수 있습니다. 작업 항목을 만들려면 작업 그룹에서 ITSM 작업을 사용 합니다.

작업 그룹은 Azure 경고에 대 한 작업을 트리거하는 모듈화 된 재사용 가능한 방법을 제공 합니다. Azure Portal에서 메트릭 경고, 활동 로그 경고 및 Azure Log Analytics 경고와 함께 작업 그룹을 사용할 수 있습니다.

> [!NOTE]
> ITSM 연결을 만든 후 동기화 프로세스가 완료 될 때까지 30 분 동안 기다려야 합니다.
> 

다음 절차를 사용 하 여 작업 항목을 만들 수 있습니다.

1. Azure Portal에서  **경고** 를 선택 합니다.
2. 화면 위쪽의 메뉴에서 **작업 관리** 를 선택 합니다.

    ![작업 관리 메뉴 항목을 보여 주는 스크린샷](media/itsmc-overview/action-groups-selection-big.png)

   **작업 그룹 만들기** 창이 나타납니다.

3. 작업 그룹을 만들려는 **구독** 및 **리소스 그룹** 을 선택 합니다. 작업 그룹의 **작업 그룹 이름** 및 **표시 이름을** 제공 합니다. **다음: 알림** 을 선택 합니다.

    ![작업 그룹 만들기 창을 보여 주는 스크린샷](media/itsmc-overview/action-groups-details.png)

4. 알림 목록에서 **다음: 작업** 을 선택 합니다.
5. 작업 목록의 **작업 유형** 목록에서 **itsm** 을 선택 합니다. 동작의 **이름을** 제공 합니다. **세부 정보 편집** 을 나타내는 펜 단추를 선택 합니다.
6. **구독** 목록에서 Log Analytics 작업 영역이 있는 구독을 선택 합니다. **연결** 목록에서 itsm 커넥터 이름을 선택 합니다. 그러면 작업 영역 이름이 뒤에 나옵니다. 예: MyITSMConnector (MyWorkspace).

7. **작업 항목** 형식을 선택 합니다.

8. 고정 값을 사용 하 여 기본 필드를 입력 하려면 **사용자 지정 템플릿 사용** 을 선택 합니다. 그렇지 않으면 **템플릿** 목록에서 기존 [템플릿을](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#template-definitions) 선택 하 고 템플릿 필드에 고정 값을 입력 합니다.

9. **각 구성 항목에 대해 개별 작업 항목 만들기** 를 선택 하면 모든 구성 항목에 자체 작업 항목이 포함 됩니다. 구성 항목 마다 작업 항목이 하나씩 있습니다. 생성 되는 경고에 따라 업데이트 됩니다.

   **각 구성 항목에 대해 개별 작업 항목 만들기** 확인란의 선택을 취소 하면 모든 경고에서 새 작업 항목을 만듭니다. 구성 항목 마다 경고가 여러 개 있을 수 있습니다.

   ![ITSM 티켓 창을 보여 주는 스크린샷](media/itsmc-overview/itsm-action-configuration.png)

10. **확인** 을 선택합니다.

Azure 경고 규칙을 만들거나 편집할 때는 ITSM 작업이 있는 작업 그룹을 사용 합니다. 경고가 트리거될 때 작업 항목이 ITSM 도구에서 만들어지거나 업데이트 됩니다.

> [!NOTE]
>
>- ITSM 작업의 가격 책정에 대 한 자세한 내용은 작업 그룹에 대 한 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/) 를 참조 하세요.
>
>
>- ITSM 작업을 사용 하 여 보낼 때 경고 규칙 정의의 간단한 설명 필드는 40 자로 제한 됩니다.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>인시던트 및 변경 요청 데이터 시각화 및 분석

연결을 설정할 때의 구성에 따라 ITSMC는 최대 120 일의 인시던트 및 변경 요청 데이터를 동기화 할 수 있습니다. 이 데이터에 대 한 로그 레코드 스키마는이 문서의 [다음 섹션](#additional-information) 에 제공 됩니다.

ITSMC 대시보드를 사용 하 여 인시던트 및 변경 요청 데이터를 시각화할 수 있습니다.

![ITSMC 대시보드를 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

또한 대시보드는 연결 문제를 분석 하기 위한 시작 지점으로 사용할 수 있는 커넥터 상태에 대 한 정보를 제공 합니다.

서비스 맵에서 영향을 받는 컴퓨터에 대해 동기화 된 인시던트를 시각화할 수도 있습니다.

서비스 맵은 Windows 및 Linux 시스템에서 애플리케이션 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 이를 통해 중요 한 서비스를 제공 하는 상호 연결 된 시스템으로 생각 되는 서버를 볼 수 있습니다. 서비스 맵는 모든 TCP 연결 아키텍처에서 서버, 프로세스 및 포트 간의 연결을 보여 줍니다. 에이전트를 설치 하는 것 외에는 구성이 필요 하지 않습니다. 자세한 내용은 [서비스 맵 사용](../insights/service-map.md)을 참조 하세요.

서비스 맵를 사용 하는 경우 다음과 같이 ITSM 솔루션에서 만들어진 서비스 데스크 항목을 볼 수 있습니다.

![Log Analytics 화면을 보여 주는 스크린샷](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


## <a name="additional-information"></a>추가 정보

### <a name="data-synced-from-your-itsm-product"></a>ITSM 제품에서 동기화 된 데이터
인시던트 및 변경 요청은 연결의 구성에 따라 ITSM 제품에서 Log Analytics 작업 영역으로 동기화 됩니다.

이 섹션에서는 ITSMC에서 수집한 데이터의 몇 가지 예를 보여 줍니다.

**ServiceDesk_CL** 필드는 Log Analytics으로 가져오는 작업 항목 형식에 따라 달라 집니다. 다음은 두 개의 작업 항목 형식에 대 한 필드 목록입니다.

**작업 항목:** **인시던트**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- 서비스 데스크 ID
- 시스템 상태
- 긴급도
- 영향
- 우선 순위
- 에스컬레이션
- 만든 사람
- 해결한 사람
- 종결한 사람
- 원본
- 할당 대상
- Category
- 제목
- Description
- 만든 날짜
- 종결한 날짜
- 해결한 날짜
- 마지막으로 수정한 날짜
- Computer


**작업 항목:** **변경 요청**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- 서비스 데스크 ID
- 만든 사람
- 종결한 사람
- 원본
- 할당 대상
- 제목
- Type
- Category
- 시스템 상태
- 에스컬레이션
- 충돌 상태
- 긴급도
- 우선 순위
- 위험
- 영향
- 할당 대상
- 만든 날짜
- 종결한 날짜
- 마지막으로 수정한 날짜
- 요청한 날짜
- 예상된 시작 날짜
- 예상된 종료 날짜
- 작업 시작 날짜
- 작업 종료 날짜
- Description
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 인시던트에 대한 출력 데이터

| Log Analytics 필드 | ServiceNow 필드 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | 시스템 상태 |
| Urgency_s |긴급도 |
| Impact_s |영향|
| Priority_s | 우선 순위 |
| CreatedBy_s | 보고자 |
| ResolvedBy_s | 해결한 사람|
| ClosedBy_s  | 종결한 사람 |
| Source_s| 연락처 유형 |
| AssignedTo_s | 할당 대상  |
| Category_s | Category |
| Title_s|  간단한 설명 |
| Description_s|  메모 |
| CreatedDate_t|  열림 |
| ClosedDate_t| closed|
| ResolvedDate_t|해결됨|
| Computer  | 구성 항목 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 변경 요청에 대한 출력 데이터

| Log Analytics | ServiceNow 필드 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 요청자 |
| ClosedBy_s | 종결한 사람 |
| AssignedTo_s | 할당 대상  |
| Title_s|  간단한 설명 |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  시스템 상태|
| Urgency_s|  긴급도 |
| Priority_s| 우선 순위|
| Risk_s| 위험|
| Impact_s| 영향|
| RequestedDate_t  | 요청한 날짜 |
| ClosedDate_t | 종결한 날짜 |
| PlannedStartDate_t  |     예상된 시작 날짜 |
| PlannedEndDate_t  |   예상된 종료 날짜 |
| WorkStartDate_t  | 실제 시작 날짜 |
| WorkEndDate_t | 실제 종료 날짜|
| Description_s | Description |
| Computer  | 구성 항목 |


## <a name="troubleshoot-itsm-connections"></a>ITSM 연결 문제 해결
- 연결 된 원본의 UI에서 연결에 실패 하 고 연결 메시지를 저장 하는 **동안 오류가 발생** 하는 경우 다음 단계를 수행 합니다.
   - ServiceNow, Cherwell 및 Provance 연결의 경우:  
     - 각 연결에 대 한 사용자 이름, 암호, 클라이언트 ID 및 클라이언트 암호를 올바르게 입력 했는지 확인 합니다.  
     - 해당 ITSM 제품에 연결을 설정 하는 데 충분 한 권한이 있는지 확인 합니다.  
   - Service Manager 연결의 경우:  
     - 웹 앱이 성공적으로 배포 되 고 하이브리드 연결이 만들어졌는지 확인 합니다. 온-프레미스 Service Manager 컴퓨터에서 연결이 성공적으로 설정 되었는지 확인 하려면 [하이브리드 연결](./itsmc-connections.md#configure-the-hybrid-connection)에 대 한 설명서에 설명 된 대로 웹 앱 URL로 이동 합니다.  

- ServiceNow의 데이터가 Log Analytics와 동기화 되지 않으면 ServiceNow 인스턴스가 절전 모드에 있지 않은지 확인 합니다. ServiceNow dev 인스턴스는 오랜 시간 동안 유휴 상태일 때 절전 모드로 전환 되는 경우가 있습니다. 문제가 발생 하지 않는 경우 문제를 보고 합니다.
- Log Analytics 경고가 발생 했지만 작업 항목이 ITSM 제품에 생성 되지 않은 경우, 구성 항목이 작업 항목에 생성/연결 되지 않거나 기타 정보에 대 한 자세한 내용은 다음 리소스를 참조 하세요.
   -  ITSMC: 솔루션은 연결, 작업 항목, 컴퓨터 등에 대 한 요약을 보여 줍니다. **커넥터 상태** 레이블이 있는 타일을 선택 합니다. 이렇게 하면 관련 쿼리를 사용 하 여 **로그 검색** 으로 이동 합니다. 자세한 내용은의를 사용 하 여 로그 레코드를 살펴보세요 `LogType_S` `ERROR` .
   - **로그 검색** 페이지: 쿼리를 사용 하 여 오류 및 관련 정보를 직접 확인 합니다 `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>웹 앱 배포 Service Manager 문제 해결
-   웹 앱 배포에 문제가 있는 경우 구독에서 리소스를 만들거나 배포할 수 있는 권한이 있는지 확인 합니다.
-   [스크립트](itsmc-service-manager-script.md)를 실행할 때 개체 **참조를 개체의 인스턴스로 설정 하지** 않으면 **사용자 구성** 섹션에서 올바른 값을 입력 했는지 확인 합니다.
-   Service bus relay 네임 스페이스를 만들지 못한 경우 필요한 리소스 공급자가 구독에 등록 되어 있는지 확인 합니다. 등록 되지 않은 경우 Azure Portal에서 service bus relay 네임 스페이스를 수동으로 만듭니다. Azure Portal에서 [하이브리드 연결을 만들](./itsmc-connections.md#configure-the-hybrid-connection) 때도 만들 수 있습니다.


## <a name="contact-us"></a>문의처

IT 서비스 관리 커넥터에 대 한 쿼리 또는 피드백이 있는 경우 microsoft에 문의 하세요 [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>다음 단계
[IT 서비스 관리 커넥터에 ITSM 제품/서비스를 추가 합니다.](./itsmc-connections.md)

