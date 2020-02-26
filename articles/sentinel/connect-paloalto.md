---
title: Palo Alto Networks 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Palo Alto Networks 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588130"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Palo Alto Networks를 Azure 센티널에 연결



이 문서에서는 Palo Alto Networks 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Palo Alto Networks 데이터 커넥터를 사용 하면 Palo Alto Networks 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure 센티널에서 Palo Alto Networks를 사용 하면 조직의 인터넷 사용에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Palo Alto Networks 로그를 Syslog 에이전트로 전달

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역에 전달 하도록 Palo Alto Networks를 구성 합니다.
1.  [CEF (Common Event Format) 구성 가이드](https://docs.paloaltonetworks.com/resources/cef) 로 이동 하 여 어플라이언스 유형별로 pdf를 다운로드 합니다. Palo Alto Networks 어플라이언스 설정 가이드의 모든 지침에 따라 CEF 이벤트를 수집 합니다. 

1.  [Syslog 모니터링 구성](https://aka.ms/asi-syslog-paloalto-forwarding) 으로 이동 하 고 2 단계와 3 단계를 수행 하 여 Palo Alto Networks 어플라이언스에서 Azure 센티널로 cef 이벤트 전달을 구성 합니다.

    1. **Syslog 서버 형식을** **BSD**로 설정 해야 합니다.

       > [!NOTE]
       > PDF의 복사/붙여넣기 작업은 텍스트를 변경 하 고 임의의 문자를 삽입할 수 있습니다. 이를 방지 하려면이 예제에서 볼 수 있는 것 처럼 텍스트를 편집기에 복사 하 고 로그 형식이 손상 될 수 있는 문자를 모두 제거 합니다.
 
        ![CEF 텍스트 복사 문제](./media/connect-cef/paloalto-text-prob1.png)

1. Palo Alto Networks 이벤트의 Log Analytics에서 관련 스키마를 사용 하려면 **CommonSecurityLog**를 검색 합니다.

1. [3 단계: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.




## <a name="next-steps"></a>다음 단계
이 문서에서는 Palo Alto Networks 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


