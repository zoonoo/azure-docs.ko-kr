---
title: Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)
description: Azure Monitor 에이전트를 사용 하 여 가상 머신에서 데이터를 수집 하는 데이터 수집 규칙을 만드는 방법을 설명 합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 8943986bf8e8c082889d3a0b18618ac54c75e6d6
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022979"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)

DCR (데이터 수집 규칙)은 Azure Monitor에 들어오는 데이터를 정의 하 고이를 보내야 하는 위치를 지정 합니다. 이 문서에서는 Azure Monitor 에이전트를 사용 하 여 가상 머신에서 데이터를 수집 하는 데이터 수집 규칙을 만드는 방법을 설명 합니다.

데이터 수집 규칙에 대 한 자세한 설명은 [Azure Monitor의 데이터 수집 규칙 (미리 보기)](data-collection-rule-overview.md)을 참조 하세요.

> [!NOTE]
> 이 문서에서는 현재 미리 보기 상태인 Azure Monitor 에이전트를 사용 하 여 가상 컴퓨터의 데이터를 구성 하는 방법을 설명 합니다. 일반 공급 되는 에이전트에 대 한 설명과이 에이전트를 사용 하 여 데이터를 수집 하는 방법에 대 한 [Azure Monitor 에이전트 개요](agents-overview.md) 를 참조 하세요.

## <a name="data-collection-rule-associations"></a>데이터 수집 규칙 연결

가상 컴퓨터에 DCR를 적용 하려면 가상 컴퓨터에 대 한 연결을 만듭니다. 가상 컴퓨터는 여러 개의 가상 컴퓨터에 연결 되어 있을 수 있으며, DCR에 연결 된 가상 컴퓨터가 여러 개 있을 수 있습니다. 이를 통해 특정 요구 사항과 일치 하는 일련의 단일 항목을 정의 하 고 적용 되는 가상 컴퓨터에만 적용할 수 있습니다. 

예를 들어 lob (기간 업무) 응용 프로그램을 실행 하는 가상 머신 집합과 SQL Server를 실행 하는 다른 환경에서 환경을 가정 합니다. 모든 가상 컴퓨터에 적용 되는 하나의 기본 데이터 수집 규칙 및 lob (기간 업무) 응용 프로그램 및 SQL Server에 대 한 데이터를 수집 하는 별도의 데이터 수집 규칙이 있을 수 있습니다. 데이터 수집 규칙에 대 한 가상 컴퓨터 연결은 다음 다이어그램과 유사 하 게 표시 됩니다.

![다이어그램은 lob (기간 업무) 응용 프로그램 및 중앙-i t-SQL Server 기본값 및 lob (기간 업무) 응용 프로그램에 대 한 데이터 수집 규칙과 관련 된 SQL Server를 호스트 하는 가상 컴퓨터를 보여 줍니다.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Azure Portal에서 규칙 및 연결 만들기

Azure Portal를 사용 하 여 데이터 수집 규칙을 만들고 구독의 가상 컴퓨터를 해당 규칙에 연결할 수 있습니다. Azure Monitor 에이전트가 자동으로 설치 되 고 아직 설치 되지 않은 가상 컴퓨터에 대해 관리 id가 만들어집니다.

> [!IMPORTANT]
> 현재 데이터 수집 규칙에서 사용자 할당 관리 id가 이미 있는 가상 머신에서 관리 id를 만들 경우 사용자 할당 id를 사용할 수 없습니다.

Azure Portal **Azure Monitor** 메뉴의 **설정** 섹션에서 **데이터 수집 규칙** 을 선택 합니다. **추가** 를 클릭 하 여 새 데이터 수집 규칙 및 할당을 추가 합니다.

[![데이터 수집 규칙](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

**추가** 를 클릭 하 여 새 규칙 및 연결 집합을 만듭니다. **규칙 이름을** 입력 하 고 **구독** 및 **리소스 그룹** 을 지정 합니다. DCR를 만들 위치를 지정 합니다. 가상 컴퓨터와 해당 연결은 테 넌 트의 모든 구독 또는 리소스 그룹에 있을 수 있습니다.

[![데이터 수집 규칙 기본 사항](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

**가상 컴퓨터** 탭에서 데이터 수집 규칙이 적용 되어야 하는 가상 컴퓨터를 추가 합니다. 사용자 환경에서 Azure virtual machines와 Azure Arc 사용 서버를 모두 나열 해야 합니다. Azure Monitor 에이전트는 아직 설치 하지 않은 가상 컴퓨터에 설치 됩니다.

[![데이터 수집 규칙 가상 컴퓨터](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

**수집 및 배달** 탭에서 **데이터 원본 추가** 를 클릭 하 여 데이터 원본 및 대상 집합을 추가 합니다. **데이터 원본 유형을** 선택 하면 선택할 항목에 해당 하는 세부 정보가 표시 됩니다. 성능 카운터의 경우 미리 정의 된 개체 집합과 샘플링 주기 중에서 선택할 수 있습니다. 이벤트의 경우 로그 나 기능 집합 및 심각도 수준을 선택할 수 있습니다. 

[![데이터 원본 기본](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


[현재 지원 되는 데이터 원본](azure-monitor-agent-overview.md#data-sources-and-destinations) 에서 다른 로그 및 성능 카운터를 지정 하거나 XPath 쿼리를 사용 하 여 이벤트를 필터링 하려면 **사용자 지정** 을 선택 합니다. 그런 다음 수집할 특정 값에 대 한 [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) 를 지정할 수 있습니다. 예제는 [샘플 DCR](data-collection-rule-overview.md#sample-data-collection-rule) 을 참조 하세요.

[![데이터 원본 사용자 지정](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

**대상** 탭에서 데이터 원본에 대 한 대상을 하나 이상 추가 합니다. Windows 이벤트 및 Syslog 데이터 원본은 Azure Monitor 로그에만 보낼 수 있습니다. 성능 카운터는 Azure Monitor 메트릭과 Azure Monitor 로그에 모두 보낼 수 있습니다.

[![대상](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

**데이터 원본 추가** 를 클릭 한 다음 **+ 만들기를 검토** 하 여 데이터 수집 규칙 및 vm 집합과의 연결에 대 한 세부 정보를 검토 합니다. 만들기 **를 클릭** 하 여 만듭니다.

> [!NOTE]
> 데이터 수집 규칙 및 연결이 생성 된 후에는 데이터를 대상으로 전송 하는 데 최대 5 분이 걸릴 수 있습니다.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>사용자 지정 XPath 쿼리를 사용 하 여 데이터 컬렉션 제한
Log Analytics 작업 영역에서 수집 된 데이터에 대 한 요금이 부과 되므로 필요한 데이터만 수집 해야 합니다. Azure Portal에서 기본 구성을 사용 하는 경우에만 수집할 이벤트를 필터링 하는 기능이 제한 됩니다. 응용 프로그램 및 시스템 로그의 경우이는 특정 심각도의 모든 로그입니다. 보안 로그의 경우이는 모두 감사 성공 또는 모든 감사 실패 로그입니다.

추가 필터를 지정 하려면 사용자 지정 구성을 사용 하 고 없는 이벤트를 필터링 하는 XPath를 지정 해야 합니다. XPath 항목은 폼으로 작성 됩니다 `LogName!XPathQuery` . 예를 들어 이벤트 ID가 1035 인 응용 프로그램 이벤트 로그에서 이벤트만 반환 하려고 할 수 있습니다. 이러한 이벤트에 대 한 XPathQuery은 `*[System[EventID=1035]]` 입니다. 응용 프로그램 이벤트 로그에서 이벤트를 검색 하려고 하므로 XPath는 다음과 같습니다. `Application!*[System[EventID=1035]]`

Windows 이벤트 로그에서 지원 되는 XPath의 제한 사항 목록은 [xpath 1.0 제한 사항](/windows/win32/wes/consuming-events#xpath-10-limitations) 을 참조 하세요.

> [!TIP]
> PowerShell cmdlet을 `Get-WinEvent` `FilterXPath` 매개 변수와 함께 사용 하 여 XPathQuery의 유효성을 테스트 합니다. 다음 스크립트는 예제를 보여 줍니다.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - 이벤트가 반환 되 면 쿼리가 유효 합니다.
> - *지정 된 선택 조건과 일치 하는 이벤트를 찾을* 수 없다는 메시지가 표시 되는 경우 쿼리는 올바르지만 로컬 컴퓨터에 일치 하는 이벤트가 없습니다.
> - *지정 된 쿼리가 잘못* 된 메시지를 받으면 쿼리 구문이 잘못 된 것입니다. 

다음 표에서는 사용자 지정 XPath를 사용 하 여 이벤트를 필터링 하는 예제를 보여 줍니다.

| 설명 |  XPath |
|:---|:---|
| 이벤트 ID가 4648 인 시스템 이벤트만 수집 |  `System!*[System[EventID=4648]]`
| 이벤트 ID가 4648이 고 프로세스 이름이 인 시스템 이벤트만 수집 consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| 이벤트 ID = 6 (드라이버 로드 됨)을 제외 하 고 시스템 이벤트 로그에서 모든 중요, 오류, 경고 및 정보 이벤트를 수집 합니다. |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| 이벤트 ID 4624 (로그온 성공)를 제외한 모든 성공 및 실패 보안 이벤트를 수집 합니다. |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>REST API를 사용 하 여 규칙 및 연결 만들기

REST API를 사용 하 여 데이터 수집 규칙 및 연결을 만들려면 다음 단계를 수행 합니다.

1. [SAMPLE DCR](data-collection-rule-overview.md#sample-data-collection-rule)에 표시 된 JSON 형식을 사용 하 여 DCR 파일을 수동으로 만듭니다.

2. [REST API](/rest/api/monitor/datacollectionrules/create#examples)를 사용 하 여 규칙을 만듭니다.

3. [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples)를 사용 하 여 데이터 수집 규칙에 대 한 각 가상 머신에 대 한 연결을 만듭니다.


## <a name="create-association-using-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 연결 만들기

리소스 관리자 템플릿을 사용 하 여 데이터 수집 규칙을 만들 수는 없지만 리소스 관리자 템플릿을 사용 하 여 Azure 가상 머신 또는 Azure Arc 사용 서버 간에 연결을 만들 수 있습니다. 샘플 템플릿 [Azure Monitor의 데이터 수집 규칙에 대 한 리소스 관리자 템플릿 샘플](./resource-manager-data-collection-rules.md) 을 참조 하세요.



## <a name="next-steps"></a>다음 단계

- [Azure Monitor 에이전트](azure-monitor-agent-overview.md)에 대해 자세히 알아보세요.
- [데이터 수집 규칙](data-collection-rule-overview.md)에 대해 자세히 알아보세요.
