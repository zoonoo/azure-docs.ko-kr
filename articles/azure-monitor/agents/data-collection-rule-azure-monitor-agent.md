---
title: Azure Monitor 에이전트를 위한 데이터 수집 구성(미리 보기)
description: Azure Monitor 에이전트를 사용하여 가상 머신에서 데이터를 수집하는 데이터 수집 규칙을 만드는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 8943986bf8e8c082889d3a0b18618ac54c75e6d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022979"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Azure Monitor 에이전트를 위한 데이터 수집 구성(미리 보기)

DCR(데이터 수집 규칙)은 Azure Monitor에 들어오는 데이터를 정의하고 데이터를 보낼 위치를 지정합니다. 이 문서에서는 Azure Monitor 에이전트를 사용하여 가상 머신에서 데이터를 수집하는 데이터 수집 규칙을 만드는 방법을 설명합니다.

데이터 수집 규칙에 대한 자세한 설명은 [Azure Monitor의 데이터 수집 규칙(미리 보기)](data-collection-rule-overview.md)을 참조하세요.

> [!NOTE]
> 이 문서에서는 현재 미리 보기 상태인 Azure Monitor 에이전트를 사용하여 가상 머신의 데이터를 구성하는 방법을 설명합니다. 일반 공급되는 에이전트에 대한 설명과 이 에이전트를 사용하여 데이터를 수집하는 방법에 대한 [Azure Monitor 에이전트 개요](agents-overview.md)를 참조하세요.

## <a name="data-collection-rule-associations"></a>데이터 수집 규칙 연결

가상 머신에 DCR를 적용하려면 가상 머신에 대한 연결을 만듭니다. 가상 머신은 여러 개의 DCR에 연결되어 있을 수 있으며, DCR에 연결된 가상 머신이 여러 개 있을 수 있습니다. 이를 통해 특정 요구 사항과 일치하는 일련의 DCR을 정의하고 적용되는 가상 머신에만 적용할 수 있습니다. 

예를 들어 lob(사업 부문) 애플리케이션을 실행하는 가상 머신 집합과 SQL Server를 실행하는 다른 환경에서 환경을 가정합니다. 모든 가상 머신에 적용되는 하나의 기본 데이터 수집 규칙과 lob(사업 부문) 애플리케이션 및 SQL Server에 대한 세부 데이터를 수집하는 별도의 데이터 수집 규칙이 있을 수 있습니다. 데이터 수집 규칙에 대한 가상 머신 연결은 다음 다이어그램과 유사하게 표시됩니다.

![다이어그램은 lob(사업 부문) 애플리케이션 및 사업 부문에 애플리케이션에 대해 central-i t-default 및 lob-app이라 부르고 SQL Server에 대해서는 central-i t-default 및 sql라 부르는 데이터 수집 규칙과 관련된 SQL Server를 호스트하는 가상 머신을 보여줍니다.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Azure Portal에서 규칙 및 연결 만들기

Azure Portal를 사용하여 데이터 수집 규칙을 만들고 구독의 가상 머신을 해당 규칙에 연결할 수 있습니다. Azure Monitor 에이전트가 자동으로 설치되고 아직 설치되지 않은 가상 머신에 대해 관리 ID가 만들어집니다.

> [!IMPORTANT]
> 현재 데이터 수집 규칙에서 사용자 할당 관리 ID가 이미 있는 가상 머신에서 관리 ID를 만들 경우 사용자 할당 ID를 사용할 수 없습니다.

Azure Portal **Azure Monitor** 메뉴의 **설정** 섹션에서 **데이터 수집 규칙** 을 선택합니다. **추가** 를 클릭하여 새 데이터 수집 규칙 및 할당을 추가합니다.

[![데이터 수집 규칙](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

**추가** 를 클릭하여 새 규칙 및 연결 집합을 만듭니다. **규칙 이름** 을 입력하고 **구독** 및 **리소스 그룹** 을 지정합니다. DCR를 만들 위치를 지정합니다. 가상 머신과 해당 연결은 테넌트의 모든 구독 또는 리소스 그룹에 있을 수 있습니다.

[![데이터 수집 규칙 기본 사항](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

**가상 머신** 탭에서 데이터 수집 규칙이 적용되어야 하는 가상 머신을 추가합니다. 사용자 환경에서 Azure 가상 머신과 Azure Arc 사용 서버를 모두 나열해야 합니다. Azure Monitor 에이전트는 아직 설치하지 않은 가상 머신에 설치됩니다.

[![데이터 수집 규칙 가상 머신](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

**수집 및 배달** 탭에서 **데이터 원본 추가** 를 클릭하여 데이터 원본 및 대상 집합을 추가합니다. **데이터 원본 형식** 을 선택하면 선택할 항목에 해당하는 세부 정보가 표시됩니다. 성능 카운터의 경우 미리 정의된 개체 집합과 샘플링 주기 중에서 선택할 수 있습니다. 이벤트의 경우 여러 로그나 기능 및 심각도 수준을 선택할 수 있습니다. 

[![데이터 원본 기본](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


[현재 지원되는 데이터 원본](azure-monitor-agent-overview.md#data-sources-and-destinations)에서 다른 로그 및 성능 카운터를 지정하거나 XPath 쿼리를 사용하여 이벤트를 필터링하려면 **사용자 지정** 을 선택합니다. 그런 다음 수집할 특정 값에 대한 [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp)를 지정할 수 있습니다. 예제는 [샘플 DCR](data-collection-rule-overview.md#sample-data-collection-rule)을 참조하세요.

[![데이터 원본 사용자 지정](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

**대상** 탭에서 데이터 원본에 대한 대상을 하나 이상 추가합니다. Windows 이벤트 및 Syslog 데이터 원본은 Azure Monitor 로그에만 보낼 수 있습니다. 성능 카운터는 Azure Monitor 메트릭과 Azure Monitor 로그에 모두 보낼 수 있습니다.

[![대상](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

**데이터 원본 추가** 를 클릭한 다음 **검토 + 만들기** 를 하여 데이터 수집 규칙 및 VM 집합과의 연결에 대한 세부 정보를 검토합니다. **만들기** 를 클릭하여 만듭니다.

> [!NOTE]
> 데이터 수집 규칙 및 연결이 생성된 후에는 데이터를 대상으로 전송하는 데 최대 5 분이 걸릴 수 있습니다.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>사용자 지정 XPath 쿼리를 사용하여 데이터 컬렉션 제한
Log Analytics 작업 영역에서 수집된 데이터에 대한 요금이 부과되므로 필요한 데이터만 수집해야 합니다. Azure Portal에서 기본 구성을 사용하는 경우에만 수집할 이벤트를 필터링하는 기능이 제한됩니다. 애플리케이션 및 시스템 로그의 경우 이는 특정 심각도의 모든 로그입니다. 보안 로그의 경우 이는 모두 감사 성공 또는 모든 감사 실패 로그입니다.

추가 필터를 지정하려면 사용자 지정 구성을 사용하고 없는 이벤트를 필터링하는 XPath를 지정해야 합니다. XPath 항목은 폼 `LogName!XPathQuery`로 작성됩니다. 예를 들어 이벤트 ID가 1035 인 애플리케이션 이벤트 로그에서 이벤트만 반환하려고 할 수 있습니다. 이러한 이벤트에 대한 XPathQuery는 `*[System[EventID=1035]]`입니다. 애플리케이션 이벤트 로그에서 이벤트를 검색하려고 하므로 XPath는 `Application!*[System[EventID=1035]]`이 될 것입니다.

Windows 이벤트 로그에서 지원되는 XPath의 제한 사항 목록은 [XPath 1.0 제한 사항](/windows/win32/wes/consuming-events#xpath-10-limitations)을 참조하세요.

> [!TIP]
> PowerShell cmdlet `Get-WinEvent`을 `FilterXPath` 매개 변수와 함께 사용하여 XPathQuery의 유효성을 테스트합니다. 다음 스크립트는 예제를 보여줍니다.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - 이벤트가 반환되면 쿼리가 유효합니다.
> - *지정된 선택 조건과 일치하는 이벤트를 찾을 수 없다* 는 메시지가 표시되는 경우 쿼리는 유효하지만 로컬 컴퓨터에 일치하는 이벤트가 없습니다.
> - *지정된 쿼리가 잘못되었습니다* 메시지를 받으면 쿼리 구문이 잘못된 것입니다. 

다음 표에서는 사용자 지정 XPath를 사용하여 이벤트를 필터링하는 예제를 보여줍니다.

| Description |  XPath |
|:---|:---|
| 이벤트 ID가 4648인 시스템 이벤트만 수집 |  `System!*[System[EventID=4648]]`
| 이벤트 ID가 4648이고 프로세스 이름이 consent.exe인 시스템 이벤트만 수집 | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| 이벤트 ID가 6(드라이버 로드 됨)인 경우를 제외하고 시스템 이벤트 로그에서 모든 중요, 오류, 경고 및 정보 이벤트를 수집 |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| 이벤트 ID 4624(로그온 성공)를 제외한 모든 성공 및 실패 보안 이벤트를 수집 |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>REST API를 사용하여 규칙 및 연결 만들기

REST API를 사용하여 데이터 수집 규칙 및 연결을 만들려면 다음 단계를 수행합니다.

1. [SAMPLE DCR](data-collection-rule-overview.md#sample-data-collection-rule)에 표시된 JSON 형식을 사용하여 DCR 파일을 수동으로 만듭니다.

2. [REST API](/rest/api/monitor/datacollectionrules/create#examples)를 사용하여 규칙을 만듭니다.

3. [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples)를 사용하여 데이터 수집 규칙에 대한 각 가상 머신에 대한 연결을 만듭니다.


## <a name="create-association-using-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 연결 만들기

리소스 관리자 템플릿을 사용하여 데이터 수집 규칙을 만들 수는 없지만 리소스 관리자 템플릿을 사용하여 Azure 가상 머신 또는 Azure Arc 사용 서버 간에 연결을 만들 수 있습니다. 샘플 템플릿으로는 [Azure Monitor의 데이터 수집 규칙에 대한 Resource Manager 템플릿 샘플](./resource-manager-data-collection-rules.md)을 참조하세요.



## <a name="next-steps"></a>다음 단계

- [Azure Monitor 에이전트](azure-monitor-agent-overview.md)에 대해 자세히 알아봅니다.
- [데이터 수집 규칙](data-collection-rule-overview.md)에 대해 자세히 알아봅니다.
