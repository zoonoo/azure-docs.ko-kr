---
title: "Log Analytics 로그 검색의 컴퓨터 그룹 | Microsoft Docs"
description: "Log Analytics의 컴퓨터 그룹을 사용하여 로그 검색 범위를 특정 컴퓨터 집합으로 한정할 수 있습니다.  이 문서에서는 컴퓨터 그룹을 만드는 데 사용할 수 있는 몇 가지 방법과 로그 검색에서의 사용 방법을 설명합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a28b9e8a-6761-4ead-aa61-c8451ca90125
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: bwren
ms.openlocfilehash: 3cc8b5c5394a031575ad0a840f57ee5e97012c17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="computer-groups-in-log-analytics-log-searches"></a>Log Analytics 로그 검색의 컴퓨터 그룹 

>[!NOTE]
> 이 문서에서는 최신 Log Analytics 쿼리 언어를 사용하는 컴퓨터 그룹 사용 방식에 대해 설명합니다.    작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우에는 컴퓨터 그룹이 다른 방식으로 작동합니다.  이 문서에서는 새 쿼리 언어에서 달라진 구문과 동작에 대해 참고 정보가 제공됩니다.  


Log Analytics의 컴퓨터 그룹을 사용하여 [로그 검색](log-analytics-log-searches.md) 범위를 특정 컴퓨터 집합으로 한정할 수 있습니다.  각 그룹에는 사용자가 정의를 사용하거나 여러 원본에서 그룹을 가져와 컴퓨터가 채워집니다.  그룹이 로그 검색에 포함된 경우 결과는 그룹의 컴퓨터에 일치하는 레코드로 한정됩니다.

## <a name="creating-a-computer-group"></a>컴퓨터 그룹 만들기
Log Analytics에서 다음 표의 방법 중 하나를 통해 컴퓨터 그룹을 만들 수 있습니다.  각 방법에 대한 자세한 내용은 아래 섹션에서 설명합니다. 

| 메서드 | 설명 |
|:--- |:--- |
| 로그 검색 |컴퓨터 목록을 반환하는 로그 검색을 만들고 결과를 컴퓨터 그룹으로 저장합니다. |
| 로그 검색 API |로그 검색 API를 사용하여 프로그래밍 방식으로 로그 검색 결과에 따라 컴퓨터 그룹을 만듭니다. |
| Active Directory |Active Directory 도메인의 구성원인 에이전트 컴퓨터의 그룹 구성원을 자동으로 검색하고 각 보안 그룹에 대해 Log Analytics에 그룹을 만듭니다. |
| WSUS |대상 그룹에 대해 자동으로 WSUS 서버나 클라이언트를 검색하고 각각에 대해 Log Analytics에 그룹을 만듭니다. |

### <a name="log-search"></a>로그 검색
로그 검색으로부터 만든 컴퓨터 그룹은 사용자가 정의한 검색 쿼리가 반환한 모든 컴퓨터를 포함합니다.  이 쿼리는 컴퓨터 그룹이 사용될 때마다 실행되므로 그룹이 만들어진 이후의 모든 변경 내용이 반영됩니다.

다음 절차를 통해 로그 검색에서 컴퓨터 그룹을 만듭니다.

1. 컴퓨터 목록을 반환하는 [로그 검색 만듭니다](log-analytics-log-searches.md).  이 검색은 쿼리에서 **Distinct Computer** 또는 **measure count() by Computer** 등과 같은 항목을 사용하여 컴퓨터의 개별 집합을 반환해야 합니다.  
2. 화면 위쪽에 있는 **저장** 단추를 클릭합니다.
3. **예**를 선택하여 **이 쿼리를 컴퓨터 그룹으로 저장**합니다.
4. 그룹의 **이름** 및 **범주**를 입력합니다.  동일한 이름 및 범주를 갖는 검색이 이미 있는 경우 덮어쓴다는 메시지가 표시됩니다.  다른 카테고리에서는 동일한 이름으로 여러 검색을 사용할 수 있습니다. 

다음은 컴퓨터 그룹으로 저장할 수 있는 검색의 예입니다.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

>[!NOTE]
> 작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우에는 새 컴퓨터 그룹을 만드는 절차가 다음과 같이 변경됩니다.
>  
> - 컴퓨터 그룹을 만드는 쿼리에 `distinct Computer`를 포함해야 합니다.  컴퓨터 그룹을 만드는 쿼리의 예제는 다음과 같습니다.<br>`Heartbeat | where Computer contains "srv" | distinct Computer `
> - 새 컴퓨터 그룹을 만들 때는 이름 외에 별칭을 지정해야 합니다.  쿼리에서 컴퓨터 그룹을 사용할 때는 아래 설명에 따라 별칭을 사용합니다.  

### <a name="log-search-api"></a>로그 검색 API
로그 검색 API를 사용하여 만들어진 컴퓨터 그룹은 로그 검색으로 만든 검색과 동일합니다.

로그 검색 API를 사용하여 컴퓨터 그룹을 만드는 것에 대한 자세한 내용은 [Log Analytics 로그 검색 REST API의 컴퓨터 그룹](log-analytics-log-search-api.md#computer-groups)을 참조하세요.

### <a name="active-directory"></a>Active Directory
Active Directory 그룹 멤버 자격을 가져오도록 Log Analytics를 구성하면 OMS 에이전트가 있는 도메인 연결 컴퓨터의 그룹 멤버 자격을 분석합니다.  컴퓨터 그룹은 Log Analytics에서 Active Directory의 각 보안 그룹에 대해 만들어지며 각 컴퓨터는 자신이 속산 보안 그룹에 해당하는 컴퓨터 그룹에 추가됩니다.  이 멤버 자격은 4시간 간격으로 계속 업데이트됩니다.  

Log Analytics **설정**의 **컴퓨터 그룹** 메뉴에서 Active Directory 보안 그룹을 가져오도록 Log Analytics를 구성할 수 있습니다.  **자동화**를 선택한 다음 **컴퓨터에서 Active Directory 그룹 멤버 자격을 가져옵니다**.  추가 구성은 필요 없습니다.

![Active Directory의 컴퓨터 그룹](media/log-analytics-computer-groups/configure-activedirectory.png)

그룹을 가져올 때는 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

### <a name="windows-server-update-service"></a>Windows Server 업데이트 서비스
WSUS 그룹 멤버 자격을 가져오도록 Log Analytics를 구성하면 OMS 에이전트가 있는 컴퓨터의 대상 그룹 멤버 자격을 분석합니다.  클라이언트 쪽 대상을 사용하는 경우 OMS에 연결되고 WSUS 대상 그룹에 속한 모든 컴퓨터의 그룹 멤버 자격을 Log Analytics로 가져옵니다. 서버 쪽을 대상으로 사용하는 경우 그룹 멤버 자격 정보를 OMS로 가져오도록 OMS 에이전트를 WSUS 서버에 설치해야 합니다.  이 멤버 자격은 4시간 간격으로 계속 업데이트됩니다. 

Log Analytics **설정**의 **컴퓨터 그룹** 메뉴에서 Active Directory 보안 그룹을 가져오도록 Log Analytics를 구성할 수 있습니다.  **Active Directory**를 선택한 다음 **컴퓨터에서 Active Directory 그룹 멤버 자격을 가져옵니다**.  추가 구성은 필요 없습니다.

![Active Directory의 컴퓨터 그룹](media/log-analytics-computer-groups/configure-wsus.png)

그룹을 가져올 때는 검색된 그룹 멤버 자격 및 가져온 그룹 수와 함께 컴퓨터 수가 메뉴에 나열됩니다.  이 링크 중 하나를 클릭하여 **ComputerGroup** 레코드와 이 정보를 반환할 수 있습니다.

## <a name="managing-computer-groups"></a>컴퓨터 그룹 관리
Log Analytics **설정**의 **컴퓨터 그룹** 메뉴에서 로그 검색 또는 로그 검색 API로부터 생성된 컴퓨터 그룹을 볼 수 있습니다.  **제거** 열에서 **x**를 클릭하여 컴퓨터 그룹을 삭제합니다.  그룹에 대한 **멤버 보기** 아이콘을 클릭하여 멤버를 반환하는 그룹의 로그 검색을 실행합니다. 

![저장된 컴퓨터 그룹](media/log-analytics-computer-groups/configure-saved.png)

그룹을 수정하려면 **범주** 및 **이름**이 같은 새 그룹을 만들어 원래의 그룹을 덮어씁니다.

## <a name="using-a-computer-group-in-a-log-search"></a>로그 검색에서 컴퓨터 그룹 사용
다음 구문을 사용하여 로그 검색에서 컴퓨터 그룹을 참조합니다.  **범주** 지정은 선택 사항이며 다른 카테고리에 이름이 같은 컴퓨터 그룹이 있는 경우에만 필수입니다. 

    $ComputerGroups[Category: Name]

검색을 실행하면 검색에 포함된 모든 컴퓨터 그룹의 멤버가 먼저 확인됩니다.  그룹이 로그 검색을 기준으로 할 경우 최상위 로그 검색을 수행하기 전에 그룹의 멤버를 반환하기 위한 검색이 실행됩니다.

일반적으로 컴퓨터 그룹은 다음 예제에서처럼 로그 검색에서 **IN** 절에서 사용됩니다.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

>[!NOTE]
> 작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우에는 다음 예제에서와 같이 컴퓨터 그룹의 별칭을 함수로 처리하는 방식으로 쿼리에서 컴퓨터 그룹을 사용합니다.
> 
>  `UpdateSummary | where Computer in (mycomputergroup)`

## <a name="computer-group-records"></a>컴퓨터 그룹 레코드
Active Directory 또는 WSUS로 만든 각각의 컴퓨터 그룹 멤버 자격에 대해 OMS 저장소에 레코드가 만들어집니다.  이 레코드의 형식은 **ComputerGroup**이며 다음 표의 속성을 갖습니다.  로그 검색 기반의 컴퓨터 그룹에 대해서는 레코드가 만들어지지 않습니다.

| 속성 | 설명 |
|:--- |:--- |
| 형식 |*ComputerGroup* |
| SourceSystem |*SourceSystem* |
| 컴퓨터 |멤버 컴퓨터의 이름입니다. |
| 그룹 |그룹의 이름입니다. |
| GroupFullName |원본 및 원본 이름을 포함하는 그룹에 대한 전체 경로입니다. |
| GroupSource |그룹을 수집해 온 원본입니다. <br><br>ActiveDirectory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName |그룹을 수집해 온 원본의 이름입니다.  Active Directory의 경우 도메인 이름이 됩니다. |
| ManagementGroupName |SCOM 에이전트의 경우 관리 그룹의 이름.  다른 에이전트의 경우 AOI-\<작업 영역 ID\>입니다. |
| TimeGenerated |컴퓨터 그룹이 만들어졌거나 업데이트된 날짜 및 시간입니다. |

## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md) 에 대해 알아봅니다.  

