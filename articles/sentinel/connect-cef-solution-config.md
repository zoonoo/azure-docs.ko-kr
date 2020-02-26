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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588453"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2 단계: CEF 메시지를 보내도록 보안 솔루션 구성

이 단계에서는 보안 솔루션 자체에서 필요한 구성 변경을 수행 하 여 CEF 에이전트로 로그를 보냅니다.

## <a name="configure-a-solution-with-a-connector"></a>커넥터를 사용 하 여 솔루션 구성

보안 솔루션에 기존 커넥터가 이미 있는 경우 다음과 같이 커넥터 관련 지침을 사용 합니다.

- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [Shift+f5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>다른 솔루션 구성
특정 보안 솔루션에 대 한 커넥터가 없는 경우 CEF 에이전트로 로그를 전달 하는 다음 일반 지침을 사용 합니다.

1. CEF 메시지를 보내도록 솔루션을 구성 하는 방법에 대 한 단계는 특정 구성 문서로 이동 합니다. 솔루션이 나열 되지 않은 경우 어플라이언스에서 Log Analytics 에이전트에 따라 필요한 형식으로 필요한 로그를 Azure 센티널 Syslog 에이전트로 보내도록 이러한 값을 설정 해야 합니다. 이러한 매개 변수는 Azure 센티널 에이전트의 Syslog 디먼 에서도 수정할 수 있는 경우 어플라이언스에서 수정할 수 있습니다.
    - 프로토콜 = TCP
    - Port = 514
    - 형식 = CEF
    - IP 주소-이 목적을 위해 전용으로 사용 하는 가상 컴퓨터의 IP 주소에 CEF 메시지를 전송 해야 합니다.

   > [!NOTE]
   > 이 솔루션은 Syslog RFC 3164 또는 RFC 5424을 지원 합니다.


1. CEF 이벤트의 Log Analytics에서 관련 스키마를 사용 하려면 `CommonSecurityLog`를 검색 합니다.

1. 3 단계: [연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

