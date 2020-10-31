---
title: Azure 센티널에 Citrix WAF 데이터 연결 | Microsoft Docs
description: Citrix WAF 데이터 커넥터를 사용 하 여 Azure 센티널로 로그를 가져오는 방법에 대해 알아봅니다. 통합 문서에서 Citrix WAF 데이터를 보고, 경고를 생성 하 고, 조사를 개선 합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102980"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Azure 센티널에 Citrix WAF 연결

> [!IMPORTANT]
> Azure 센티널의 Citrix WAF (웹 응용 프로그램 방화벽) 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다. 이 기능은 서비스 수준 계약 없이 제공 됩니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Citrix WAF (웹 응용 프로그램 방화벽) 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Citrix WAF 데이터 커넥터를 사용 하면 Citrix WAF 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Citrix WAF CEF 로그를 Azure 센티널에 연결 하면 각 로그에 대해 검색 및 상관 관계, 경고 및 위협 인텔리전스 보강을 활용할 수 있습니다.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Syslog 에이전트로 Citrix WAF 로그 전달  

Citrix WAF는 Azure 센티널 작업 영역으로 로그를 전달 하는 Log Analytics 에이전트가 설치 된 Linux 기반 로그 전달 서버 (rsyslog 또는 Syslog를 실행 하는)로 CEF 형식의 Syslog 메시지를 보냅니다.

1. 이러한 로그 전달 서버가 없는 경우 [다음 지침](connect-cef-agent.md) 을 참조 하 여 실행 합니다.

1. Citrix에서 제공 하는 지침에 따라 [WAF를 구성](https://support.citrix.com/article/CTX234174)하 고, [cef 로깅을 구성](https://support.citrix.com/article/CTX136146)하 고, [로그를 로그 전달자에 전송 하도록 구성](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)합니다. 로그 전달자 컴퓨터의 IP 주소에서 TCP 포트 514로 로그를 전송 해야 합니다.

1. [이러한 지침](connect-cef-verify.md)을 사용 하 여 연결의 유효성을 검사 하 고 데이터 수집을 확인 합니다. 로그가 Log Analytics 나타날 때까지 최대 20 분이 걸릴 수 있습니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정 되 면 데이터는 **로그** 의 **Azure 센티널** 섹션 아래 *CommonSecurityLog* 테이블에 표시 됩니다.

Log Analytics에서 Citrix WAF 로그를 쿼리하려면 `CommonSecurityLog` 쿼리 창의 맨 위에를 입력 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Citrix WAF를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.