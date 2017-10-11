---
title: "OMS Log Analytics에서 데이터 원본 구성 | Microsoft Docs"
description: "데이터 원본은 Log Analytics가 에이전트 및 기타 연결된 원본에서 수집하는 데이터를 정의합니다.  이 문서에서는 Log Analytics에서 데이터 원본을 사용하는 방법에 대한 개념을 제공하고, 데이터 원본을 구성하는 방법을 자세히 설명하며, 사용 가능한 여러 데이터 원본을 요약합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: 00d030a502cf70ea9a5dea767f560cdf2919573e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="data-sources-in-log-analytics"></a>Log Analytics의 데이터 원본
Log Analytics는 OMS 작업 영역의 연결된 원본에서 데이터를 수집하여 OMS 리포지토리에 저장합니다.  각 원본에서 수집되는 데이터는 구성한 데이터 원본에 의해 정의됩니다.  OMS 리포지토리의 데이터는 레코드 집합으로 저장됩니다.  각 데이터 원본은 각각 고유한 속성 집합이 있는 특정 유형의 레코드를 만듭니다.

![Log Analytics 데이터 수집](./media/log-analytics-data-sources/overview.png)

OMS 솔루션도 연결된 원본에서 데이터를 수집하고 OMS 리포지토리에 레코드를 만들지만 이는 데이터 원본과 다릅니다.  솔루션은 솔루션 갤러리에서 작업 영역에 추가할 수 있으며 일반적으로 OMS 포털에서 추가 분석 도구를 제공합니다.  

## <a name="summary-of-data-sources"></a>데이터 원본 요약
다음 표에는 Log Analytics에서 현재 사용할 수 있는 데이터 원본이 나와 있습니다.  각각 해당 데이터 원본에 대한 세부 정보를 제공하는 별도의 문서에 대한 링크가 있습니다.

| 데이터 원본 | 이벤트 유형 | 설명 |
|:--- |:--- |:--- |
| [사용자 지정 로그](log-analytics-data-sources-custom-logs.md) |\<LogName\>_CL |로그 정보가 포함된 Windows 또는 Linux 에이전트의 텍스트 파일. |
| [Windows 이벤트 로그](log-analytics-data-sources-windows-events.md) |이벤트 |Windows 컴퓨터의 이벤트 로그에서 수집된 이벤트. |
| [Windows 성능 카운터](log-analytics-data-sources-performance-counters.md) |Perf |Windows 컴퓨터에서 수집한 성능 카운터. |
| [Linux 성능 카운터](log-analytics-data-sources-performance-counters.md) |Perf |Linux 컴퓨터에서 수집한 성능 카운터. |
| [IIS 로그](log-analytics-data-sources-iis-logs.md) |W3CIISLog |W3C 형식의 IIS(인터넷 정보 서비스) 로그. |
| [Syslog](log-analytics-data-sources-syslog.md) |syslog |Windows 또는 Linux 컴퓨터의 Syslog 이벤트. |

## <a name="configuring-data-sources"></a>데이터 원본 구성
Log Analytics **설정**의 **데이터** 메뉴에서 데이터 원본을 구성합니다.  모든 구성은 OMS 작업 영역의 모든 연결된 원본으로 전달됩니다.  현재 이 구성에서 에이전트를 제외할 수는 없습니다.

![Windows 이벤트 구성](./media/log-analytics-data-sources/configure-events.png)

1. OMS 콘솔에서 화면 상단에 있는 **설정** 버튼 또는 **설정** 타일을 클릭합니다.
2. **데이터**를 선택합니다.
3. 구성할 데이터 원본을 클릭합니다.
4. 해당 구성에 대한 세부 정보를 보려면 위 표에서 각 데이터 원본에 대한 설명서 링크를 따릅니다.

> [!NOTE]
> 현재 Azure Portal에서 Log Analytics 데이터 원본을 구성할 수 없습니다.

## <a name="data-collection"></a>데이터 수집
데이터 원본 구성은 몇 분 이내에 Log Analytics에 직접 연결된 에이전트로 전달됩니다.  지정된 데이터는 에이전트에서 수집되어 각 데이터 원본에 특정한 간격으로 Log Analytics에 직접 전달됩니다.  이러한 세부 사항은 각 데이터 원본에 대한 설명서를 참조하세요.

연결된 관리 그룹 내 SCOM(System Center Operations Manager) 에이전트의 경우 데이터 원본 구성은 관리 팩으로 변환되어 기본적으로 5분마다 관리 그룹에 전달됩니다.  에이전트는 일반적인 방식으로 관리 팩을 다운로드하고 지정된 데이터를 수집합니다. 데이터 원본에 따라, 데이터가 관리 서버로 전송된 다음 관리 서버가 Log Analytics로 데이터를 전달하거나 에이전트가 관리 서버를 통하지 않고 Log Analytics로 데이터를 전송합니다. 자세한 내용은 [OMS 기능 및 솔루션에 대한 데이터 수집 정보](log-analytics-add-solutions.md#data-collection-details)를 참조하십시오.  SCOM과 OMS의 연결 및 구성 전달 주기 수정에 대한 자세한 내용은 [System Center Operations Manager와의 통합 구성](log-analytics-om-agents.md)에서 확인할 수 있습니다.

에이전트는 Log Analytics 또는 Operations Manager에 연결할 수 없으면 데이터를 계속 수집하다가 연결이 설정되면 데이터를 전달합니다.  데이터 양이 클라이언트의 최대 캐시 크기에 도달하거나 에이전트가 24시간 내에 연결을 설정할 수 없는 경우 데이터가 손실될 수 있습니다.

## <a name="log-analytics-records"></a>Log Analytics 레코드
Log Analytics에서 수집된 모든 데이터는 OMS 리포지토리에 레코드로 저장됩니다.  여러 데이터 원본에서 수집된 레코드는 고유한 속성 집합이 있으며 해당 **Type** 속성으로 식별됩니다.  각 레코드 유형에 대한 자세한 내용은 각 데이터 원본 및 솔루션에 대한 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계
* Log Analytics에 기능을 추가하고 OMS 리포지토리로 데이터를 수집하는 [솔루션](log-analytics-add-solutions.md) 에 대해 알아봅니다.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md) 에 대해 알아봅니다.  
* 데이터 원본 및 솔루션에서 수집된 중요한 데이터를 사전에 알리도록 [경고](log-analytics-alerts.md)를 구성합니다.
