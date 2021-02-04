---
title: Azure 센티널 Preview에 CEF 데이터를 연결 하도록 보안 솔루션 구성 | Microsoft Docs
description: Azure 센티널에 CEF 데이터를 연결 하도록 보안 솔루션을 구성 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 07685cccf5a8dce99ae13e6df5186a5da44b1338
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559414"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2 단계: CEF 메시지를 보내도록 보안 솔루션 구성

이 단계에서는 보안 솔루션 자체에서 필요한 구성 변경을 수행 하 여 CEF 에이전트로 로그를 보냅니다.

## <a name="configure-a-solution-with-a-connector"></a>커넥터를 사용 하 여 솔루션 구성

보안 솔루션에 기존 커넥터가 이미 있는 경우 다음과 같이 커넥터 관련 지침을 사용 합니다.

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Akamai 보안 이벤트](connect-akamai-security-events.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Forcepoint 제품](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [Imperva WAF 게이트웨이](connect-imperva-waf-gateway.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [WireX 네트워크 법률 분석 플랫폼](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>다른 솔루션 구성

특정 보안 솔루션에 대 한 커넥터가 없는 경우 CEF 에이전트로 로그를 전달 하는 다음 일반 지침을 사용 합니다.

1. CEF 메시지를 보내도록 솔루션을 구성 하는 방법에 대 한 단계는 특정 구성 문서로 이동 합니다. 솔루션이 나열 되지 않은 경우 어플라이언스에서 Log Analytics 에이전트에 따라 필요한 형식으로 필요한 로그를 Azure 센티널 Syslog 에이전트로 보내도록 이러한 값을 설정 해야 합니다. 이러한 매개 변수는 Azure 센티널 에이전트의 Syslog 디먼 에서도 수정할 수 있는 경우 어플라이언스에서 수정할 수 있습니다.
    - 프로토콜 = TCP
    - 포트 = 514
    - 형식 = CEF
    - IP 주소-이 목적을 위해 전용으로 사용 하는 가상 컴퓨터의 IP 주소에 CEF 메시지를 전송 해야 합니다.

   이 솔루션은 Syslog RFC 3164 또는 RFC 5424을 지원 합니다.

1. Log Analytics에서 CEF 이벤트를 검색 하려면 `CommonSecurityLog` 쿼리 창에를 입력 합니다.

1. 3 단계: [연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.

> [!NOTE]
> **TimeGenerated 필드의 원본 변경**
>
> - 기본적으로 Log Analytics 에이전트는 스키마의 *Timegenerated* 필드를 Syslog 디먼에서 에이전트가 이벤트를 받은 시간으로 채웁니다. 따라서 원본 시스템에서 이벤트가 생성된 시간은 Azure Sentinel에 기록되지 않습니다.
>
> - 그러나 스크립트를 다운로드 하 여 실행 하는 다음 명령을 실행할 수 있습니다 `TimeGenerated.py` . 이 스크립트는 에이전트에서 수신 된 시간 대신 원본 시스템에서 *Timegenerated* 필드를 이벤트의 원래 시간으로 채우도록 Log Analytics 에이전트를 구성 합니다.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 CEF 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./tutorial-detect-threats-built-in.md)을 시작합니다.