---
title: Azure 센티널에 Fortinet 데이터 연결 | Microsoft Docs
description: Fortinet 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 3ed83f794cdb92f709cbf5c0ea236a5a9b4c187b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610542"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Azure 센티널에 Fortinet 연결



이 문서에서는 Fortinet 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Fortinet data connector를 사용 하면 Fortinet 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure 센티널에서 Fortinet를 사용 하면 조직의 인터넷 사용에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Syslog 에이전트로 Fortinet 로그 전달

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역으로 전달 하도록 Fortinet을 구성 합니다.

1. Fortinet 어플라이언스에서 CLI를 열고 다음 명령을 실행 합니다.

        config log syslogd setting
        set format cef
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - 서버 **ip 주소** 를 에이전트의 ip 주소로 바꿉니다.
    - **Syslog 포트** 를 **514** 로 설정 하거나 에이전트에 설정 된 포트를 설정 합니다.
    - 초기 FortiOS 버전에서 CEF 형식을 사용 하도록 설정 하려면 **csv disable**명령을 실행 해야 할 수 있습니다.
 
   > [!NOTE] 
   > 자세한 내용은 [Fortinet 문서 라이브러리로](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)이동 합니다. 버전을 선택 하 고 **교** 사용 및 **로그 메시지 참조**를 사용 합니다.

1. Fortinet 이벤트에 대 한 Azure Monitor Log Analytics에서 관련 스키마를 사용 하려면 `CommonSecurityLog`를 검색 합니다.

1. [3 단계: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Fortinet 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


