---
title: 시스코 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Cisco 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588402"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>시스코 ASA를 Azure 센티넬에 연결



이 문서에서는 Cisco ASA 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Cisco ASA 데이터 커넥터를 사용하면 Cisco ASA 로그를 Azure Sentinel과 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 Cisco ASA를 사용하면 조직의 인터넷 사용에 대한 더 많은 정보를 제공하고 보안 운영 기능을 향상시킬 수 있습니다. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>시스코 ASA로 시스코 ASA 로그전달

Cisco ASA는 CEF를 지원하지 않으므로 로그가 Syslog로 전송되고 Azure Sentinel 에이전트는 CEF 로그인 것처럼 구문 분석하는 방법을 알고 있습니다. Sisco ASA를 구성하여 Syslog 에이전트를 통해 Syslog 메시지를 Azure 작업 영역으로 전달하도록 구성합니다.

1. 외부 [Syslog 서버로 Syslog 메시지 보내기로](https://aka.ms/asi-syslog-cisco-forwarding)이동하여 지침에 따라 연결을 설정합니다. 다음과 같은 메시지가 표시될 때 이러한 매개 변수를 사용합니다.
    - **포트를** 514 또는 에이전트에서 설정한 포트로 설정합니다.
    - 에이전트의 IP 주소로 **syslog_ip** 설정합니다.

1. Cisco 이벤트에 대해 로그 분석에서 관련 스키마를 `CommonSecurityLog`사용하려면 을 검색합니다.

1. [3단계 계속: 연결 유효성 검사](connect-cef-verify.md).




## <a name="next-steps"></a>다음 단계
이 문서에서는 Cisco ASA 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


