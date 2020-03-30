---
title: CEF 데이터를 Azure Sentinel 미리 보기에 연결하도록 보안 솔루션 구성 | 마이크로 소프트 문서
description: CEF 데이터를 Azure Sentinel에 연결하도록 보안 솔루션을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588453"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2단계: CEF 메시지를 보낼 보안 솔루션 구성

이 단계에서는 보안 솔루션 자체에서 필요한 구성 변경을 수행하여 CEF 에이전트에 로그를 보냅니다.

## <a name="configure-a-solution-with-a-connector"></a>커넥터로 솔루션 구성

보안 솔루션에 이미 기존 커넥터가 있는 경우 다음과 같이 커넥터 관련 지침을 사용합니다.

- [Check Point](connect-checkpoint.md)
- [시스코](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>다른 솔루션 구성
특정 보안 솔루션에 대한 커넥터가 없는 경우 CEF 에이전트에 로그를 전달하기 위한 다음 일반 지침을 사용합니다.

1. CEF 메시지를 보내도록 솔루션을 구성하는 방법에 대한 단계는 특정 구성 문서로 이동하십시오. 솔루션이 나열되지 않은 경우 어플라이언스에서 로그 분석 에이전트를 기반으로 필요한 형식으로 필요한 로그를 Azure Sentinel Syslog 에이전트로 전송하도록 이러한 값을 설정해야 합니다. Azure Sentinel 에이전트의 Syslog 데몬에서도 이러한 매개 변수를 수정하는 한 어플라이언스에서 이러한 매개 변수를 수정할 수 있습니다.
    - 프로토콜 = TCP
    - 포트 = 514
    - 형식 = CEF
    - IP 주소 - CEF 메시지를 이 목적을 위해 전용한 가상 시스템의 IP 주소로 보내야 합니다.

   > [!NOTE]
   > 이 솔루션은 Syslog RFC 3164 또는 RFC 5424를 지원합니다.


1. CEF 이벤트에 대한 로그 분석에서 관련 스키마를 `CommonSecurityLog`사용하려면 을 검색합니다.

1. 3단계 계속: [연결의 유효성을 검사합니다.](connect-cef-verify.md)

## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

