---
title: Imperva WAF 게이트웨이 어플라이언스를 Azure 센티널에 연결 | Microsoft Docs
description: Imperva WAF 게이트웨이 커넥터를 사용 하 여 Imperva WAF 로그를 Azure 센티널로 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Imperva WAF 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698436"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Imperva WAF 게이트웨이 어플라이언스를 Azure 센티널에 연결

> [!IMPORTANT]
> Imperva WAF 게이트웨이 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타, 미리 보기 또는 아직 일반 공급으로 출시 되지 않은 Azure 기능에 적용 되는 추가 약관은 [Microsoft Azure 미리 보기에](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 대 한 추가 사용 약관을 참조 하세요.

이 문서에서는 Imperva WAF 게이트웨이 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Imperva WAF 게이트웨이 데이터 커넥터를 사용 하면 Imperva WAF 게이트웨이 로그를 Azure 센티널과 쉽게 연결할 수 있으므로 통합 문서에서 데이터를 보고,이를 사용 하 여 사용자 지정 경고를 만들고,이를 통합 하 여 조사를 개선할 수 있습니다. Imperva WAF 게이트웨이와 Azure 센티널 간의 통합을 통해 CEF 형식의 Syslog, Linux 기반 로그 전달자 및 Log Analytics 에이전트를 사용 합니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 센티널 작업 영역에 대 한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역에 대 한 공유 키에 대 한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Azure 센티널로 Imperva WAF 게이트웨이 로그 보내기

Azure 센티널로 로그를 가져오려면 Imperva WAF 게이트웨이 어플라이언스를 구성 하 여 CEF 형식의 Syslog 메시지를 Linux 기반 로그 전달 서버 (rsyslog 또는 Syslog를 실행 하는)로 보냅니다. 이 서버에는 Log Analytics 에이전트가 설치 되 고 에이전트가 Azure 센티널 작업 영역으로 로그를 전달 합니다.

1. Azure 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. **데이터 커넥터** 갤러리에서 **Imperva waf 게이트웨이 (미리 보기)** 를 선택한 다음 **커넥터 페이지를 엽니다**.

1. **지침** 탭의 **구성** 아래에 있는 지침을 따르세요.

    1. **1. Linux Syslog 에이전트 구성** -로그 전달 자가 아직 실행 되지 않은 경우 또는 다른 것이 필요한 경우이 단계를 수행 합니다. 자세한 지침 및 설명은 1 단계: Azure 센티널 설명서에서 [로그 전달자 배포](connect-cef-agent.md) 를 참조 하세요.

    1. **2 미만. CEF (Common Event Format) 로그를 Syslog 에이전트로 전달** -이 커넥터를 사용 하려면 **IMPERVA SecureSphere MX** 관리 콘솔에서 **작업 인터페이스** 및 **작업 집합** 을 만들어야 합니다. Imperva의 지침에 따라 [Azure 센티널에 Imperva WAF 게이트웨이 경고 로깅을 사용 하도록 설정](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)합니다. 이 구성에는 다음 요소가 포함 되어야 합니다.
        - 로그 대상 – 로그 전달 서버의 호스트 이름 및/또는 IP 주소
        - 프로토콜 및 포트-TCP 514
        - 로그 형식 – CEF
        - 로그 형식-모두 사용 가능

    1. **3. 연결 유효성 검사** -커넥터 페이지에서 명령을 복사 하 고 로그 전달자에서 실행 하 여 데이터 수집을 검증 합니다. 자세한 지침 및 설명은 3 단계: Azure 센티널 설명서에서 [연결 유효성 검사](connect-cef-verify.md) 를 참조 하세요.

        로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 의 **Azure 센티널** 섹션 아래 *CommonSecurityLog* 테이블에 표시 됩니다.

Log Analytics에서 Imperva WAF 게이트웨이 데이터를 쿼리하려면 다음을 쿼리 창에 복사 하 여 선택한 대로 다른 필터를 적용 합니다.

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

더 유용한 쿼리 샘플은 커넥터 페이지의 **다음 단계** 탭을 참조 하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Imperva WAF 게이트웨이를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대 한 가시성을 확보](quickstart-get-visibility.md)하는 방법을 알아보세요.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.