---
title: Azure Sentinel에 Citrix WAF 데이터 연결| Microsoft Docs
description: Citrix WAF 데이터 커넥터를 사용하여 Azure Sentinel에 로그를 가져오는 방법을 알아봅니다. 통합 문서에서 Citrix WAF 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
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
ms.openlocfilehash: 309d2cfb47c61d516be19d110cb7ade1f3c63e18
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695175"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Citrix WAF을 Azure Sentinel에 연결

> [!IMPORTANT]
> Azure Sentinel의 Citrix WAF(웹 애플리케이션 방화벽) 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 규약 없이 이용할 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서에서는 Citrix WAF(웹 애플리케이션 방화벽) 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Citrix WAF 데이터 커넥터를 사용하면 Citrix WAF 로그를 Azure Sentinel에 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Citrix WAF CEF 로그를 Azure Sentinel에 연결하면 각 로그에 대한 검색 및 상관 관계, 경고 및 위협 인텔리전스 보강을 활용할 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Syslog 에이전트로 Citrix WAF 로그 전달  

Citrix WAF는 로그를 Azure Sentinel 작업 영역으로 전달하는 Log Analytics 에이전트가 설치된 Linux 기반 로그 전달 서버(rsyslog 또는 syslog-ng 실행)로 CEF 형식의 Syslog 메시지를 보냅니다.

1. 로그 전달 서버가 없는 경우, [본 지침](connect-cef-agent.md)을 참조하여 실행합니다.

1. Citrix 제공 지침에 따라 [WAF 구성하기](https://support.citrix.com/article/CTX234174), [CEF 로깅 구성하기](https://support.citrix.com/article/CTX136146) 및 [로그 전달자로 로그 전송 구성하기](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html)를 수행합니다. 로그 전달자 컴퓨터의 IP 주소에서 TCP 포트 514로 로그를 보내야합니다.

1. [본 지침](troubleshooting-cef-syslog.md#validate-cef-connectivity)을 사용하여 연결의 유효성을 검사하고 데이터 수집을 확인합니다. 로그가 Log Analytics에 표시되는 데는 최대 20분이 걸립니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정된 후 *CommonSecurityLog* 테이블에서 **Azure Sentinel** 섹션의 **로그** 에 데이터가 표시됩니다.

Log Analytics에서 Citrix WAF 로그를 쿼리하려면 쿼리 창 상단에 `CommonSecurityLog`을 입력합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Citrix WAF를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.