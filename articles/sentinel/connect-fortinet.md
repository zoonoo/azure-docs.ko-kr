---
title: Fortinet 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Fortinet 데이터를 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588198"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>포르티트에서 Azure 센티넬에 연결



이 문서에서는 Fortinet 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Fortinet 데이터 커넥터를 사용하면 Fortinet 로그를 Azure Sentinel과 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 Fortinet을 사용하면 조직의 인터넷 사용에 대한 더 많은 정보를 얻을 수 있으며 보안 작업 기능이 향상됩니다. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>포티티트 로그를 시슬로그 에이전트로 전달

Syslog 에이전트를 통해 CEF 형식으로 Syslog 메시지를 Azure 작업 영역으로 전달하도록 Fortinet을 구성합니다.

1. Fortinet 어플라이언스에서 CLI를 열고 다음 명령을 실행합니다.

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - 서버 **IP 주소를** 에이전트의 IP 주소로 바꿉니다.
    - **syslog 포트를** **514로** 설정하거나 에이전트에 설정된 포트를 설정합니다.
    - 초기 FortiOS 버전에서 CEF 형식을 사용하려면 명령 집합 **csv를 비활성화해야**할 수 있습니다.
 
   > [!NOTE] 
   > 자세한 내용은 [Fortinet 문서 라이브러리로](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)이동하십시오. 버전을 선택하고 **핸드북** 및 **로그 메시지 참조를**사용합니다.

1. Fortinet 이벤트에 Azure 모니터 로그 분석에서 관련 스키마를 `CommonSecurityLog`사용하려면 을 검색합니다.

1. [3단계 계속: 연결 유효성 검사](connect-cef-verify.md).


## <a name="next-steps"></a>다음 단계
이 문서에서는 Fortinet 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


