---
title: "Azure Log Analytics의 IT Service Management Connector | Microsoft Docs"
description: "IT Service Management Connector를 사용하여 Azure Log Analytics에서 ITSM 작업 항목을 중앙에서 모니터링 및 관리하고 문제를 빠르게 해결합니다."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 411d6103852cbf534d3c420d5ea7b2146df5164e
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>IT Service Management Connector(미리 보기)를 사용하여 ITSM 작업 항목을 중앙에서 관리

![IT Service Management Connector 기호](./media/log-analytics-itsmc/itsmc-symbol.png)

IT Service Management Connector는 지원되는 ITSM(IT Service Management) 제품/서비스와 Log Analytics 간의 양방향 통합을 제공합니다.  이 연결을 통해 Log Analytics 경고 또는 로그 레코드에 따라 ITSM 제품에서 인시던트, 경고 또는 이벤트를 만들 수 있습니다. 또한 이 커넥터는 인시던트와 같은 데이터를 가져오며, ITSM 제품에서 OMS Log Analytics로 요청을 변경합니다.

IT Service Management Connector를 사용하여 다음을 수행할 수 있습니다.

  - 선택한 ITSM 도구에서 인시던트 관리 사례와 작업 경고를 통합합니다.
    - OMS 경고 및 로그 검색을 통해 ITSM에 작업 항목(예: 경고, 이벤트, 인시던트)을 만듭니다.
    - 작업 그룹의 ITSM 작업을 통해 Azure 활동 로그 경고에 따라 작업 항목을 만듭니다. 
  
  - 조직 전체에서 사용되는 모니터링, 로그 및 서비스 관리 데이터를 통합합니다.
    - ITSM 도구의 인시던트 및 변경 요청 데이터를 Log Analytics 작업 영역의 관련 로그 데이터와 상호 연결합니다.   
    - 인시던트, 변경 요청 및 영향을 받는 시스템에 대한 개요는 최상위 대시보드를 통해 볼 수 있습니다.
    - Log Analytics 쿼리를 작성하여 서비스 관리 데이터에 대한 정보를 얻습니다.
      
## <a name="adding-the-it-service-management-connector-solution"></a>IT Service Management Connector 솔루션 추가

[솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명하는 프로세스를 사용하여 IT Service Management Connector 솔루션을 Log Analytics 작업 영역에 추가합니다.

솔루션 갤러리에 표시되는 IT Service Management Connector 타일:

![커넥터 타일](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

성공적으로 추가한 후 **OMS** > **설정** > **연결된 원본** 아래에 IT Service Management Connector가 표시됩니다.

![ITSMC가 연결되어 있습니다.](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> 기본적으로 IT Service Management Connector는 24시간마다 한 번씩 연결 데이터를 새로 고칩니다. 적용한 편집 내용 또는 템플릿 업데이트에 대해 연결 데이터를 즉시 새로 고치려면 연결 옆에 표시된 새로 고침 단추를 클릭합니다.

 ![ITSMC 새로 고침](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-connection-with-your-itsm-software"></a>ITSM 소프트웨어와의 연결 구성

IT Service Management Connector 솔루션은 **System Center Service Manager**, **ServiceNow**, **Provance** 및 **Cherwell**에 대한 연결을 지원합니다. 다음을 사용하여 연결을 구성합니다.

- [SCSM(System Center Service Manager)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>솔루션 사용

ITSM 소프트웨어 세부 정보를 사용하여 IT Service Management Connector를 구성하면 Connector에서 연결된 ITSM 제품/서비스로부터 데이터 수집을 시작합니다. ITSM 제품/서비스의 인시던트 수 및 변경 요청에 따라 몇 분 안에 초기 동기화를 완료해야 합니다. 

> [!NOTE]
> - IT Service Management Connector 솔루션에서 ITSM 제품으로부터 가져온 데이터는 Log Analytics에서 **ServiceDesk_CL** 유형의 로그 레코드로 표시됩니다.
> - 이 로그 레코드에는 ITSM 제품에서 가져온 두 가지 종류의 데이터, 즉 인시던트 또는 변경 요청인 **ServiceDeskWorkItemType_s**라는 필드가 있습니다

## <a name="data-synced-from-itsm-product"></a>ITSM 제품에서 동기화된 데이터
인시던트 및 변경 요청은 ITSM 제품에서 Log Analytics 작업 영역으로 동기화됩니다. 다음 정보는 IT Service Management Connector에서 수집된 데이터 예제를 보여 줍니다.

> [!NOTE]
> Log Analytics로 가져온 작업 항목 형식에 따라 **ServiceDesk_CL**에는 다음 필드가 포함됩니다.

**작업 항목:** **인시던트**  
ServiceDeskWorkItemType_s="Incident"

**필드**

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
- 설명
- 만든 날짜
- 종결한 날짜
- 해결한 날짜
- 마지막으로 수정한 날짜
- 컴퓨터


**작업 항목:** **변경 요청**

ServiceDeskWorkItemType_s="ChangeRequest"

**필드**
- ServiceDeskConnectionName
- 서비스 데스크 ID
- 만든 사람
- 종결한 사람
- 원본
- 할당 대상
- 제목
- 형식
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
- 설명
- 컴퓨터

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow 인시던트에 대한 출력 데이터

| OMS 필드 | ITSM 필드 |
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
| Description_s|  참고 사항 |
| CreatedDate_t|  열림 |
| ClosedDate_t| closed|
| ResolvedDate_t|해결됨|
| 컴퓨터  | 구성 항목 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 변경 요청에 대한 출력 데이터

| OMS 필드 | ITSM 필드 |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 요청자 |
| ClosedBy_s | 종결한 사람 |
| AssignedTo_s | 할당 대상  |
| Title_s|  간단한 설명 |
| Type_s|  형식 |
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
| Description_s | 설명 |
| 컴퓨터  | 구성 항목 |

**ITSM 데이터에 대한 샘플 Log Analytics 화면:**

![Log Analytics 화면](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="it-service-management-connector--integration-with-other-oms-solutions"></a>IT Service Management Connector - 다른 OMS 솔루션과의 통합

IT Service Management Connector는 현재 서비스 맵 솔루션과의 통합을 지원합니다.

서비스 맵은 Windows 및 Linux 시스템에서 응용 프로그램 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑합니다. 따라서 생각처럼 중요한 서비스를 제공하는 상호 연결된 시스템으로 서버를 볼 수 있습니다. 서비스 맵은 서버, 프로세스 및 에이전트 설치 이외에 구성이 필요 없는 TCP 연결 아키텍처의 포트 간 연결을 보여 줍니다. 추가 정보: [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md)

서비스 맵 솔루션도 사용하는 경우 다음 예와 같이 ITSM 솔루션에서 만들어진 서비스 데스크 항목을 볼 수 있습니다.

![서비스 맵 통합](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>OMS 경고에 대한 ITSM 작업 항목 만들기

ITSM Connector 솔루션을 사용하면 다음과 같이 연결된 ITSM 도구에서 작업 항목 만들기를 트리거하는 OMS 경고를 구성할 수 있습니다.

1. **로그 검색** 창에서 로그 검색 쿼리를 실행하여 데이터를 봅니다. 쿼리 결과는 작업 항목의 원본입니다.
2. **로그 검색**에서 **경고**를 클릭하여 **경고 규칙 추가** 페이지를 엽니다.

    ![Log Analytics 화면](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. **경고 규칙 추가** 창에서 **이름**, **심각도**, **검색 쿼리** 및 **경고 조건**(기간/메트릭 측정)에 대한 필수 정보를 제공합니다.
4. **ITSM 작업**에 대해 **예**를 선택합니다.
5. **연결 선택** 목록에서 ITSM 연결을 선택합니다.
6. 필요에 따라 세부 정보를 제공합니다.
7. 이 경고의 각 로그 항목에 대해 별도 작업 항목을 만들려면 **각 로그 항목에 대해 개별 작업 항목 만들기** 확인란을 선택합니다.

    또는

    이 경고에서 제한없는 수의 로그 항목에 대해 하나의 작업 항목만 만들려면 이 확인란을 선택 취소합니다.

7. **Save**를 클릭합니다.

만든 OMS 경고는 **설정**>**경고** 아래에 표시됩니다. 지정된 경고 조건이 충족되면 해당 ITSM 연결의 작업 항목이 만들어집니다.

## <a name="create-itsm-work-items-from-oms-logs"></a>OMS 로그에서 ITSM 작업 항목 만들기

다음과 같이 로그 레코드에서 직접 연결된 ITSM 원본의 작업 항목을 만들 수도 있습니다.

1. **로그 검색**에서 필요한 데이터를 검색하고 세부 정보를 선택한 후 **작업 항목 만들기**를 클릭합니다.

    **ITSM 작업 항목 만들기** 창이 나타납니다.

    ![Log Analytics 화면](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   다음 세부 정보를 추가합니다.

  - **작업 항목 제목**: 작업 항목의 제목입니다.
  - **작업 항목 설명**: 새 작업 항목에 대한 설명입니다.
  - **영향을 받는 컴퓨터**: 이 로그 데이터가 있는 컴퓨터의 이름입니다.
  - **연결 선택**: 이 작업 항목을 만들려는 ITSM 연결입니다.
  - **작업 항목**: 작업 항목의 유형입니다.

3. 인시던트에 대한 기존 작업 항목 템플릿을 사용하려면 **템플릿을 기준으로 작업 항목 생성** 옵션에서 **예**를 클릭한 다음 **만들기**를 클릭합니다.

    또는

    사용자 지정한 값을 제공하려는 경우 **아니요**를 클릭합니다.

4. **연락처 유형**, **영향**, **긴급도**, **범주** 및 **하위 범주** 텍스트 상자에 적절한 값을 입력하고 **만들기**를 클릭합니다.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure Alerts에서 ITSM 작업 항목 만들기
ITSM Connector가 이제 작업 그룹과 통합되었습니다. [작업 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md)은 Azure Alerts를 위해 모듈 방식으로 다시 사용할 수 있는 방법을 제공합니다. 작업 그룹의 ITSM 작업은 기존 ITSM Connector 솔루션을 사용하여 ITSM 제품에 작업 항목을 만듭니다.

1. Azure Portal에서 **모니터**를 클릭합니다.
2. 왼쪽 창에서 **작업 그룹**을 클릭합니다.

    ![작업 그룹](media/log-analytics-itsmc/ActionGroups.png)

3. 작업 그룹에 대해 **이름** 및 **짧은 이름**을 제공합니다. 작업 그룹을 만들려는 **리소스 그룹** 및 **구독**을 선택합니다.

    ![작업 그룹 세부 정보](media/log-analytics-itsmc/ActionGroupsDetail.png)

4. 작업 목록의 **작업 유형** 드롭다운에서 **ITSM**을 선택합니다. 해당 작업에 대한 **이름**을 제공하고 **세부 정보 편집**을 클릭합니다.


5. Log Analytics 작업 영역이 있는 **구독**을 선택합니다. **연결**, 즉 ITSM Connector 이름 뒤에 나오는 작업 영역 이름을 선택합니다. 예를 들어 "MyITSMMConnector(MyWorkspace)"와 같습니다.

    ![ITSM 작업 세부 정보](./media/log-analytics-itsmc/ITSMActionDetails.png)

6. 드롭다운에서 **작업 항목** 유형을 선택합니다.
7. 기존 템플릿을 사용하거나 ITSM 제품에 필요한 필드를 채우도록 선택합니다.
8. **확인**

Azure 경고 규칙을 만들거나 편집할 때는 ITSM 작업이 있는 작업 그룹을 사용합니다. 경고가 트리거되면 작업 항목이 ITSM 도구에 만들어집니다. 

>[!NOTE]
>현재 활동 로그 경고만 ITSM 작업을 지원합니다. 다른 Azure 경고의 경우 이 작업은 작동하지 않습니다.
>


## <a name="troubleshoot-itsm-connections-in-oms"></a>OMS에서 ITSM 연결 문제 해결
1.  연결된 원본의 UI에서 **연결을 저장하는 동안 오류 발생** 메시지와 함께 연결에 실패하는 경우 다음 단계를 수행합니다.
 - ServiceNow, Cherwell 및 Provence 연결의 경우
    - 각 연결에 대한 사용자 이름, 암호 클라이언트 ID 및 클라이언트 비밀을 올바르게 입력했는지 확인합니다.
    - 해당 ITSM 제품에 연결하는 데 충분한 권한이 있는지 확인합니다.
 - Service Manager 연결의 경우
     - 웹앱이 성공적으로 배포되고 하이브리드 연결이 만들어졌는지 확인합니다. 온-프레미스 Service Manager 컴퓨터와의 연결이 성공적으로 설정되었는지 확인하려면 [하이브리드 연결](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) 설정 설명서에 따라 웹앱 URL을 방문합니다.
     
2.  ServiceNow의 데이터가 Log Analytics와 동기화되지 않으면 ServiceNow 인스턴스가 중지 상태가 아닌지 확인합니다. ServiceNow Dev 인스턴스가 오랫동안 유휴 상태일 때 중지 상태로 전환되는 경우가 있습니다. 다른 문제를 보고합니다.
3.  OMS 경고가 발생하지만 ITSM 제품에 작업 항목이 만들어지지 않거나 구성 항목이 작업 항목에 또는 기타 일반적인 정보에 대해 만들어지거나 연결되지 않는 경우 다음 위치를 확인합니다.
 -  **IT Service Management Connector 솔루션**: 연결/작업 항목/컴퓨터 등에 대한 요약을 보여 줍니다. **커넥터 상태**를 보여 주는 타일을 클릭하면 **로그 검색**으로 이동합니다. 자세한 정보는 LogType_S가 ERROR(오류)인 로그 레코드를 살펴봅니다.
 - 또는 *Type=ServiceDeskLog_CL* 쿼리를 사용하여 **로그 검색** 페이지에서 오류/관련 정보를 직접 확인합니다.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager 웹앱 배포 문제 해결
1.  웹앱 배포 문제가 있는 경우 리소스 만들기/배포에서 설명한 구독에 충분한 권한이 있는지 확인합니다.
2.  [스크립트](log-analytics-itsmc-service-manager-script.md)를 실행할 때 **"개체 참조가 개체의 인스턴스로 설정되지 않았습니다."** 오류가 발생하는 경우 **사용자 구성** 섹션에서 유효한 값을 입력했는지 확인합니다.
3.  Service Bus Relay 네임스페이스 만들기에 실패한 경우 구독에 필요한 리소스 공급자가 등록되어 있는지 확인합니다. 등록되지 않은 경우 Azure Portal에서 수동으로 서비스 버스 릴레이 네임스페이스를 만듭니다. Azure Portal에서 [하이브리드 연결을 만드는](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) 동안 만들 수도 있습니다.


## <a name="contact-us"></a>문의처

IT Service Management Connector에 대해 질문이나 의견이 있는 경우 [omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com)으로 문의하세요.

## <a name="next-steps"></a>다음 단계
[ITSM 제품/서비스를 IT Service Management Connector에 추가](log-analytics-itsmc-connections.md).

