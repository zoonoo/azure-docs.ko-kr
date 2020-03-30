---
title: 팔로 알토 네트웍스 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: 팔로알토 네트웍스 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588130"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>팔로 알토 네트웍스를 Azure 센티넬에 연결



이 문서에서는 팔로 알토 네트웍스 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. 팔로 알토 네트웍스 데이터 커넥터를 사용하면 팔로 알토 네트웍스 로그를 Azure Sentinel과 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 팔로 알토 네트웍스를 사용하면 조직의 인터넷 사용에 대한 더 많은 정보를 제공하고 보안 운영 기능을 향상시킬 수 있습니다. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>팔로 알토 네트웍스로 전달 시슬로그 에이전트로 기록

팔로 알토 네트웍스를 구성하여 CEF 형식으로 Syslog 메시지를 Syslog 에이전트를 통해 Azure 작업 영역으로 전달하도록 구성합니다.
1.  일반 [이벤트 형식(CEF) 구성 가이드로](https://docs.paloaltonetworks.com/resources/cef) 이동하여 어플라이언스 유형에 대한 PDF를 다운로드합니다. CEF 이벤트를 수집하기 위해 팔로 알토 네트웍스 어플라이언스를 설정하는 가이드의 모든 지침을 따르십시오. 

1.  [Syslog 모니터링 구성으로](https://aka.ms/asi-syslog-paloalto-forwarding) 이동하여 2단계와 3단계를 수행하여 팔로알토 네트웍스 어플라이언스에서 Azure Sentinel로 의 CEF 이벤트 전달을 구성합니다.

    1. **Syslog 서버 형식을** **BSD로**설정해야 합니다.

       > [!NOTE]
       > PDF의 복사/붙여넣기 작업은 텍스트를 변경하고 임의의 문자를 삽입할 수 있습니다. 이 작업을 방지하려면 이 예제에서 볼 수 있듯이 텍스트를 편집기에 복사하고 로그 형식을 붙여넣기 전에 중단될 수 있는 문자를 제거합니다.
 
        ![CEF 텍스트 복사 문제](./media/connect-cef/paloalto-text-prob1.png)

1. 팔로 알토 네트워크 이벤트에 대한 로그 분석에서 관련 스키마를 사용하려면 **CommonSecurityLog**를 검색합니다.

1. [3단계 계속: 연결 유효성 검사](connect-cef-verify.md).




## <a name="next-steps"></a>다음 단계
이 문서에서는 팔로 알토 네트웍스 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


