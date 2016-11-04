---
title: OMS(Operations Management Suite)의 경고 관리 솔루션 | Microsoft Docs
description: Log Analytics의 경고 관리 솔루션을 사용하여 환경의 모든 경고를 분석할 수 있습니다.  OMS 내에서 생성된 경고를 통합하는 것 외에도 연결된 SCOM(System Center Operations Manager) 관리 그룹에서 Log Analytics로 경고를 가져옵니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2016
ms.author: bwren

---
# <a name="alert-management-solution-in-operations-management-suite-(oms)"></a>OMS(Operations Management Suite)의 경고 관리 솔루션
![경고 관리 아이콘](media/log-analytics-solution-alert-management/icon.png) 경고 관리 솔루션을 사용하여 환경의 모든 경고를 분석할 수 있습니다.  OMS 내에서 생성된 경고를 통합하는 것 외에도 연결된 SCOM(System Center Operations Manager) 관리 그룹에서 Log Analytics로 경고를 가져옵니다.  여러 관리 그룹을 가진 환경의 경우 경고 관리 솔루션은 모든 관리 그룹 전체에 걸쳐 경고의 통합된 뷰를 제공합니다.

## <a name="prerequisites"></a>필수 조건
* SCOM에서 경고를 가져오려면 이 솔루션에서 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md)에 설명된 프로세스를 사용하여 OMS 작업 영역과 SCOM 관리 그룹을 연결해야 합니다.  

## <a name="configuration"></a>구성
[솔루션 추가](log-analytics-add-solutions.md)에 설명된 프로세스를 사용하여 경고 관리 솔루션을 OMS 작업 영역에 추가합니다.  추가 구성은 필요 없습니다.

## <a name="management-packs"></a>관리 팩
SCOM 관리 그룹이 OMS 작업 영역에 연결된 경우 이 솔루션을 추가할 때 다음 관리 팩이 SCOM에 설치됩니다.  이 관리 팩에 대한 구성 또는 유지 관리는 필요 없습니다.  

* Microsoft System Center Advisor 경고 관리(Microsoft.IntelligencePacks.AlertManagement)

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md)을 참조하세요.

## <a name="data-collection"></a>데이터 수집
### <a name="agents"></a>에이전트
다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원 | 설명 |
|:--- |:--- |:--- |
| [Windows 에이전트](log-analytics-windows-agents.md) |아니요 |직접 Windows 에이전트는 SCOM 경고를 생성하지 않습니다. |
| [Linux 에이전트](log-analytics-linux-agents.md) |아니요 |직접 Linux 에이전트는 SCOM 경고를 생성하지 않습니다. |
| [SCOM 관리 그룹](log-analytics-om-agents.md) |예 |SCOM 에이전트에 대해 생성된 경고는 관리 그룹에 전달된 다음 Log Analytics에 전달됩니다.<br><br>SCOM에서 Log Analytics로 직접 연결은 필요하지 않습니다. 경고 데이터는 관리 그룹에서 OMS 저장소로 전달됩니다. |
| [Azure 저장소 계정](log-analytics-azure-storage.md) |아니요 |SCOM 경고는 Azure 저장소 계정에 저장되지 않습니다. |

### <a name="collection-frequency"></a>수집 빈도
OMS 내에서 생성된 경고는 즉시 솔루션에 제공됩니다.  경고 데이터는 매 3 분마다 SCOM 관리 그룹에서 
Log Analytics로 전송됩니다.  

## <a name="using-the-solution"></a>솔루션 사용
경고 관리 솔루션을 OMS 작업 영역에 추가할 때 OMS 대시보드에 **경고 관리** 타일이 추가됩니다.  이 타일은 마지막 24 시간 이내에 생성된 현재 활성 경고 수에 대한 카운트와 그래픽 표현을 표시합니다.  이 시간 범위를 변경할 수 없습니다.

![경고 관리 타일](media/log-analytics-solution-alert-management/tile.png)

**경고 관리** 타일을 클릭하여 **경고 관리** 대시보드를 엽니다.  대시보드는 다음 표의 열을 포함하고 있습니다.  각 열은 지정된 범위 및 시간 범위에 대한 열의 기준과 일치하는 카운트별로 상위 열 개의 경고를 나열합니다.  열 아래쪽의 **모두 보기** 를 클릭하거나 열 제목을 클릭하여 전체 목록을 제공하는 로그 검색을 실행할 수 있습니다.

| 열 | 설명 |
|:--- |:--- |
| 중요한 알림 |경고 이름별로 그룹화된 중요 심각도를 가진 모든 경고.  경고 이름을 클릭하여 해당 경고에 대한 모든 레코드를 반환하는 로그 검색을 실행합니다. |
| 경고 알림 |경고 이름별로 그룹화된 경고의 심각도를 가진 모든 경고입니다.  경고 이름을 클릭하여 해당 경고에 대한 모든 레코드를 반환하는 로그 검색을 실행합니다. |
| 활성 SCOM 경고 |경고를 생성한 소스별로 그룹화된 *닫힘* 이외의 상태를 가진 모든 SCOM 경고입니다. |
| 모든 활성 경고 |경고 이름별로 그룹화된 심각도를 가진 모든 경고입니다. *닫힘*이외의 상태를 가진 SCOM 경고만 포함합니다. |

오른쪽으로 스크롤하면 대시보드는 일반 쿼리 여러 개를 나열하며, 이 쿼리를 클릭하면 경고 데이터에 대한 [로그 검색](log-analytics-log-searches.md) 을 수행할 수 있습니다.

![경고 관리 대시보드](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>범위 및 시간 범위
기본적으로 경고 관리 솔루션에서 분석된 경고의 범위는 마지막 7일 이내에 생성된 모든 연결된 관리 그룹에서 나온 것입니다.  

![경고 관리 범위](media/log-analytics-solution-alert-management/scope.png)

* 분석에 포함된 관리 그룹을 변경하려면 대시보드 위쪽에서 **범위** 를 클릭합니다.  모든 연결된 관리 그룹에 대해 **전역**을 선택하거나 **관리 그룹별**을 선택하여 단일 관리 그룹을 선택할 수 있습니다.
* 경고의 시간 범위를 변경하려면 대시보드 위쪽에서 **데이터 기반** 을 선택합니다.  지난 7 일, 1 일 또는 6 시간 내에 생성된 경고를 선택할 수 있습니다.  또는 **사용자 지정** 을 선택하고 사용자 지정 날짜 범위를 지정할 수 있습니다.

## <a name="log-analytics-records"></a>Log Analytics 레코드
경고 관리 솔루션은 **경고**유형을 가진 모든 레코드를 분석합니다.  또한 SCOM에서 경고를 가져오며 유형이 **경고**이고 SourceSystem이 **OpsManager**인 각각에 대해 해당 레코드를 만듭니다.  이러한 레코드는 다음 표의 속성을 가집니다.  

| 속성 | 설명 |
|:--- |:--- |
| 형식 |*경고* |
| SourceSystem |*OpsManager* |
| AlertContext |경고가 XML 형식으로 생성되게 하는 데이터 항목에 대한 세부 정보입니다. |
| AlertDescription |경고에 대한 자세한 설명입니다. |
| AlertId |경고의 GUID입니다. |
| AlertName |경고의 이름입니다. |
| AlertPriority |경고의 우선 순위 수준입니다. |
| AlertSeverity |경고의 심각도 수준입니다. |
| AlertState |경고의 최신 해결 상태입니다. |
| LastModifiedBy |경고를 마지막으로 수정한 사용자의 이름입니다. |
| ManagementGroupName |경고를 생성한 관리 그룹의 이름입니다. |
| RepeatCount |확인된 후 같은 모니터링 대상 개체에 대해 같은 경고가 생성된 횟수입니다. |
| ResolvedBy |경고를 확인한 사용자의 이름입니다. 경고가 아직 확인되지 않은 경우 비어 있습니다. |
| SourceDisplayName |경고를 생성한 모니터링 개체의 표시 이름입니다. |
| SourceFullName |경고를 생성한 모니터링 개체의 전체 이름입니다. |
| TicketId |경고에 대해 티켓을 할당하기 위해 SCOM 환경이 프로세스와 통합되는 경우 경고에 대한 티켓 ID입니다.  비어 있으면 티켓 ID가 할당되지 않습니다. |
| TimeGenerated |경고가 만들어진 날짜 및 시간입니다. |
| TimeLastModified |경고가 마지막 변경된 날짜 및 시간입니다. |
| TimeRaised |경고가 생성된 날짜 및 시간입니다. |
| TimeResolved |경고가 확인된 날짜 및 시간입니다. 경고가 아직 확인되지 않은 경우 비어 있습니다. |

## <a name="sample-log-searches"></a>샘플 로그 검색
다음 표는 이 솔루션에 의해 수집된 경고 레코드에 대한 샘플 로그 검색을 제공합니다.  

| 쿼리 | 설명 |
|:--- |:--- |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR |지난 24 시간 동안 발생한 중요한 알림 |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR |지난 24 시간 동안 발생한 중요한 경고 |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR &#124; measure count() as Count by SourceDisplayName |지난 24 시간 동안 발생한 활성 경고를 가진 소스 |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed |지난 24 시간 동안 발생했고 여전히 활성화되어 있는 중요 경고 |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed |지난 24 시간 발생했고 지금은 해결된 경고 |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; measure count() as Count by AlertSeverity |지난 1 일 동안 발생했고 심각도별로 그룹화된 경고 |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; sort RepeatCount desc |지난 1 일 동안 발생했고 반복 횟수 값별로 정렬된 경고 |

## <a name="next-steps"></a>다음 단계
* Log Analytics에서 경고 생성에 대한 자세한 내용은 [Log Analytics의 경고](log-analytics-alerts.md) 에 관하여 알아보세요.

<!--HONumber=Oct16_HO2-->


