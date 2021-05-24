---
title: Azure Monitor에서 Log Analytics 에이전트를 사용하여 IIS 로그 수집
description: IIS(인터넷 정보 서비스)는 Azure Monitor에서 수집할 수 있는 로그 파일에 사용자 활동을 저장합니다.  이 문서에서는 IIS 로그 수집을 구성하는 방법을 설명하고, Azure Monitor에 생성되는 레코드에 대한 자세한 정보를 제공합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 87cb19daa23c9fcca601771a9fe168b98be02627
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586270"
---
# <a name="collect-iis-logs-with-log-analytics-agent-in-azure-monitor"></a>Azure Monitor에서 Log Analytics 에이전트를 사용하여 IIS 로그 수집
IIS(인터넷 정보 서비스)는 Log Analytics 에이전트에서 수집하고 [Azure Monitor 로그](../data-platform.md)로 저장할 수 있는 로그 파일에 사용자 활동을 저장합니다.

> [!IMPORTANT]
> 이 문서에서는 Azure Monitor에서 사용하는 에이전트 중 하나인 [Log Analytics 에이전트](./log-analytics-agent.md)를 사용하여 IIS 로그를 수집하는 방법을 설명합니다. 다른 에이전트는 다른 데이터를 수집하고 다르게 구성됩니다. 사용 가능한 에이전트 및 수집할 수 있는 데이터 목록은 [Azure Monitor 에이전트 개요](../agents/agents-overview.md)를 참조하세요.

![IIS 로그](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>IIS 로그 구성
Azure Monitor는 IIS에서 생성된 로그 파일의 항목을 수집하므로 [로깅에 대해 IIS를 구성](/previous-versions/orphan-topics/ws.11/hh831775(v=ws.11))해야 합니다.

Azure Monitor는 W3C 형식으로 저장된 IIS 로그 파일만 지원하며 사용자 지정 필드 또는 IIS 고급 로깅을 지원하지 않습니다. NCSA 또는 IIS 네이티브 형식의 로그는 수집하지 않습니다.

Log Analytics 에이전트의 [에이전트 구성 메뉴](../agents/agent-data-sources.md#configuring-data-sources)에서 Azure Monitor의 IIS 로그를 구성합니다.  **W3C 형식 IIS 로그 파일 수집** 을 선택하는 것 외에 다른 구성은 필요 없습니다.


## <a name="data-collection"></a>데이터 수집
Azure Monitor는 로그 타임스탬프가 변경될 때마다 각 에이전트에서 IIS 로그 항목을 수집합니다. **5분** 마다 로그를 읽습니다. 어떤 이유로 새 파일이 생성될 때 IIS가 롤오버 시간 전에 타임스탬프를 업데이트하지 않으면 새 파일이 생성된 후에 항목이 수집됩니다. 새 파일 만들기에 대한 빈도는 기본적으로 하루에 한 번인 IIS 사이트에 대한 **로그 파일 롤오버 일정** 설정에 의해 제어됩니다. **시간별** 로 설정되어 있는 경우 Azure Monitor는 1시간마다 로그를 수집합니다. **일별** 로 설정되어 있는 경우 Azure Monitor는 24시간마다 로그를 수집합니다.

> [!IMPORTANT]
> **로그 파일 롤오버 일정** 을 **시간별** 로 설정하는 것이 좋습니다. **일별** 로 설정되어 있는 경우 하루에 한 번만 수집되므로 데이터가 급증할 수 있습니다.

## <a name="iis-log-record-properties"></a>IIS 로그 레코드 속성
IIS 로그 레코드는 **W3CIISLog** 형식이며, 다음 표의 속성이 있습니다.

| 속성 | Description |
|:--- |:--- |
| Computer |이벤트가 수집된 컴퓨터의 이름입니다. |
| cIP |클라이언트의 IP 주소입니다. |
| csMethod |GET 또는 POST와 같은 요청 메서드입니다. |
| csReferer |사용자가 현재 사이트로 이동하는 데 사용된 링크가 있던 사이트입니다. |
| csUserAgent |클라이언트의 브라우저 유형입니다. |
| csUserName |서버에 액세스한 인증된 사용자의 이름입니다. 익명 사용자는 하이픈으로 표시됩니다. |
| csUriStem |웹 페이지와 같은 요청의 대상입니다. |
| csUriQuery |클라이언트가 수행하려고 한 쿼리입니다(있는 경우). |
| ManagementGroupName |Operations Manager 에이전트의 관리 그룹 이름.  다른 에이전트의 경우 AOI-\<workspace ID\>입니다. |
| RemoteIPCountry |클라이언트 IP 주소의 국가/지역입니다. |
| RemoteIPLatitude |클라이언트 IP 주소의 위도입니다. |
| RemoteIPLongitude |클라이언트 IP 주소의 경도입니다. |
| scStatus |HTTP 상태 코드입니다. |
| scSubStatus |하위 상태 오류 코드입니다. |
| scWin32Status |Windows 상태 코드입니다. |
| sIP |웹 서버의 IP 주소입니다. |
| SourceSystem |OpsMgr |
| sPort |클라이언트가 연결된 서버의 포트입니다. |
| sSiteName |IIS 사이트의 이름입니다. |
| TimeGenerated |항목이 로깅된 날짜 및 시간입니다. |
| TimeTaken |요청을 처리할 시간의 길이(밀리초)입니다. |
| csHost | 호스트 이름. |
| csBytes | 서버에서 받은 바이트 수입니다. |

## <a name="log-queries-with-iis-logs"></a>IIS 로그를 사용한 로그 쿼리
다음 표에는 IIS 로그 레코드를 검색하는 로그 쿼리의 여러 예제가 나와 있습니다.

| 쿼리 | Description |
|:--- |:--- |
| W3CIISLog |모든 IIS 로그 레코드 |
| W3CIISLog &#124; where scStatus==500 |반환 상태가 500인 모든 IIS 로그 레코드입니다. |
| W3CIISLog &#124; summarize count() by cIP |클라이언트 IP 주소별 IIS 로그 항목 수 |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |호스트 www\.contoso.com의 URL별 IIS 로그 항목 수입니다. |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |각 IIS 컴퓨터에서 받은 총 바이트 수 |

## <a name="next-steps"></a>다음 단계
* 분석을 위해 다른 [데이터 원본](../agents/agent-data-sources.md)을 수집하도록 Azure Monitor를 구성합니다.
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../logs/log-query-overview.md)에 대해 알아봅니다.
