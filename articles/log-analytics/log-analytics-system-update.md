<properties
    pageTitle="Log Analytics에서 시스템 업데이트 평가 솔루션 | Microsoft Azure"
    description="서버 인프라에 누락된 업데이트를 적용할 수 있도록 Log Analytics에서 시스템 업데이트 솔루션을 사용할 수 있습니다."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="system-update-assessment-solution-in-log-analytics"></a>Log Analytics에서 시스템 업데이트 평가 솔루션

서버 인프라에 누락된 업데이트를 적용할 수 있도록 Log Analytics에서 시스템 업데이트 솔루션을 사용할 수 있습니다. 솔루션을 설치하면 OMS의 **개요** 페이지에서 **시스템 업데이트 평가** 타일을 사용하여 모니터링된 서버에서 누락된 업데이트를 볼 수 있습니다.

누락된 업데이트가 발견되면 세부 정보에 **업데이트** 대시보드에 표시됩니다. **업데이트** 대시보드를 사용하여 누락된 업데이트를 작업하고 필요로 하는 서버에 적용하도록 계획할 수 있습니다.

## <a name="installing-and-configuring-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

- [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 OMS 작업 영역에 시스템 업데이트 평가 솔루션을 추가합니다.  추가 구성은 필요 없습니다.

## <a name="system-update-data-collection-details"></a>시스템 업데이트 데이터 수집 세부 정보

시스템 업데이트 평가는 사용하도록 설정한 에이전트를 통해 메타데이터 및 상태 데이터를 수집합니다.

다음 표에서는 데이터 수집 방법 및 시스템 업데이트 평가를 위해 데이터가 수집되는 방식에 대한 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | SCOM 에이전트 | Azure 저장소 | SCOM 필요? | 관리 그룹을 통해 전송되는 SCOM 에이전트 데이터 | 수집 빈도 |
|---|---|---|---|---|---|---|
|Windows|![예](./media/log-analytics-system-update/oms-bullet-green.png)|![예](./media/log-analytics-system-update/oms-bullet-green.png)|![아니요](./media/log-analytics-system-update/oms-bullet-red.png)|            ![아니요](./media/log-analytics-system-update/oms-bullet-red.png)|![예](./media/log-analytics-system-update/oms-bullet-green.png)| 하루에 최소 2회 및 업데이트를 설치하고 15분 후|

다음 표에서는 시스템 업데이트 평가에 의해 수집된 데이터 형식 예제를 보여 줍니다.

|**데이터 형식**|**필드**|
|---|---|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|시스템 상태|StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### <a name="to-work-with-updates"></a>업데이트를 작성하려면

1. **개요** 페이지에서 **시스템 업데이트 평가** 타일을 클릭합니다.  
    ![시스템 업데이트 평가 타일](./media/log-analytics-system-update/sys-update-tile.png)
2. **업데이트** 대시보드에서 업데이트 범주를 봅니다.  
    ![업데이트 대시보드](./media/log-analytics-system-update/sys-updates02.png)
3. 페이지 오른쪽으로 스크롤하여 **Windows 중요/보안 업데이트** 블레이드를 표시한 다음 **분류**에서 **보안 업데이트**를 클릭합니다.  
    ![보안 업데이트](./media/log-analytics-system-update/sys-updates03.png)
4. 로그 검색 페이지에 인프라의 서버에서 누락이 발견된 보안 업데이트 목록에 대한 다양한 정보가 표시됩니다. **목록**을 클릭하여 업데이트에 대한 자세한 정보를 봅니다.  
    ![검색 결과 - 목록](./media/log-analytics-system-update/sys-updates04.png)
5. 로그 검색 페이지에 각 업데이트에 대한 자세한 정보가 표시됩니다. KBID 번호 옆에 있는 **보기**를 클릭하면 Microsoft 지원 웹 사이트에서 해당 문서를 볼 수 있습니다.  
    ![로그 검색 결과 - KB 보기](./media/log-analytics-system-update/sys-updates05.png)
6. 웹 브라우저가 새 탭에서 업데이트에 대한 Microsoft 지원 웹 페이지를 엽니다. 누락된 업데이트에 대한 정보를 봅니다.  
    ![Microsoft 지원 웹 페이지](./media/log-analytics-system-update/sys-updates06.png)
7. 찾은 정보를 사용하여 수동으로 누락된 업데이트를 적용하는 계획을 만들거나 나머지 단계를 계속 수행하여 자동으로 업데이트를 적용할 수 있습니다.
8. 자동으로 누락된 업데이트를 적용하려면 **업데이트** 대시보드로 돌아간 후 **업데이트 실행**에서 **업데이트 실행을 예약하려면 클릭하세요.**를 클릭합니다.  
    ![업데이트 실행 - 예약됨 탭](./media/log-analytics-system-update/sys-updates07.png)
9. **업데이트 실행** 페이지의 **예약됨** 탭에서 **추가**를 클릭하여 새 업데이트 실행을 만듭니다.  
    ![예약됨 탭 - 추가](./media/log-analytics-system-update/sys-updates08.png)
10. **새 업데이트 실행** 페이지에서 업데이트 실행에 대한 이름을 입력하고, 개별 컴퓨터 또는 컴퓨터 그룹을 추가하고, 일정을 정의한 후 **저장**을 클릭합니다.  
    ![새 업데이트 실행](./media/log-analytics-system-update/sys-updates09.png)
11. **업데이트 실행** 페이지의 **예약됨** 탭에 예약한 새 업데이트 실행이 표시됩니다.  
    ![예약됨 탭](./media/log-analytics-system-update/sys-updates10.png)
12. 업데이트 실행을 시작하면 그에 대한 정보가 **실행 중** 탭에 표시됩니다.  
    ![실행 중 탭](./media/log-analytics-system-update/sys-updates11.png)
13. 업데이트 실행이 완료된 후에는 **완료됨** 탭에 상태가 표시됩니다.
14. 업데이트 실행에서 업데이트를 적용한 경우는 **Windows 중요/보안 업데이트** 블레이드에서 업데이트 수가 감소된 것을 알 수 있습니다.  
    ![Windows 중요/보안 업데이트 블레이드 - 업데이트 수 감소](./media/log-analytics-system-update/sys-updates12.png)



## <a name="next-steps"></a>다음 단계

- [로그를 검색](log-analytics-log-searches.md) 하여 자세한 시스템 업데이트 데이터를 볼 수 있습니다.



<!--HONumber=Oct16_HO2-->


