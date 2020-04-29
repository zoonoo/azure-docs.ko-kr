---
title: Azure 센티널에 Zscaler 데이터 연결 | Microsoft Docs
description: Zscaler 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77587994"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Azure 센티널에 Zscaler 인터넷 액세스 연결

> [!IMPORTANT]
> Azure 센티널의 Zscaler data 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Zscaler 인터넷 액세스 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Zscaler data connector를 사용 하면 ZIA (Zscaler Internet Access) 로그를 Azure 센티널과 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure 센티널에서 Zscaler를 사용 하면 조직의 인터넷 사용에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>CEF 메시지를 보내도록 Zscaler 구성

1. Zscaler 어플라이언스에서 어플라이언스는 Log Analytics 에이전트에 따라 필요한 형식으로 필요한 로그를 Azure 센티널 Syslog 에이전트로 보내도록 이러한 값을 설정 해야 합니다. 이러한 매개 변수는 Azure 센티널 에이전트의 Syslog 디먼 에서도 수정할 수 있는 경우 어플라이언스에서 수정할 수 있습니다.
    - 프로토콜 = TCP
    - 포트 = 514
    - 형식 = CEF
    - IP 주소-이 목적을 위해 전용으로 사용 하는 가상 컴퓨터의 IP 주소에 CEF 메시지를 전송 해야 합니다.
 자세한 내용은 [Zscaler 및 Azure 센티널 배포 가이드](https://aka.ms/ZscalerCEFInstructions)를 참조 하세요.
 
   > [!NOTE]
   > 이 솔루션은 Syslog RFC 3164 또는 RFC 5424을 지원 합니다.


1. CEF 이벤트의 Log Analytics에서 관련 스키마를 사용 하려면를 검색 `CommonSecurityLog`합니다.
1. [3 단계: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 Zscaler 인터넷 액세스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


