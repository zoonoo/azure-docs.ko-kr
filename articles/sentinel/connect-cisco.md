---
title: Azure 센티널에 Cisco 데이터 연결 | Microsoft Docs
description: Cisco 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 67a76f1fcd4cfcdd372407ae62eb03d5905cda68
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610661"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Azure 센티널에 Cisco GLOBAL.ASA 연결



이 문서에서는 Azure 센티널에 Cisco GLOBAL.ASA 어플라이언스를 연결 하는 방법을 설명 합니다. Cisco GLOBAL.ASA 데이터 커넥터를 사용 하면 Azure 센티널과 Cisco GLOBAL.ASA 로그를 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure 센티널에서 Cisco를 사용 하면 조직의 인터넷 사용에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Syslog 에이전트로 Cisco GLOBAL.ASA 로그 전달

Cisco는 CEF를 지원 하지 않으므로 로그가 Syslog로 전송 되 고 Azure 센티널 에이전트는 CEF logs 처럼 구문 분석 하는 방법을 알고 있습니다. Syslog 에이전트를 통해 Azure 작업 영역에 Syslog 메시지를 전달 하도록 Cisco를 구성 합니다.

1. [외부 syslog 서버에 syslog 메시지 보내기](https://aka.ms/asi-syslog-cisco-forwarding)로 이동 하 여 지침에 따라 연결을 설정 합니다. 메시지가 표시 되 면 다음 매개 변수를 사용 합니다.
    - **포트** 를 514으로 설정 하거나 에이전트에서 설정한 포트를 설정 합니다.
    - **Syslog_ip** 를 에이전트의 ip 주소로 설정 합니다.

1. Cisco 이벤트의 Log Analytics에서 관련 스키마를 사용 하려면 `CommonSecurityLog`를 검색 합니다.

1. [3 단계: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Cisco GLOBAL.ASA 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


