---
title: Zscaler 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: Zscaler 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587994"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Zscaler 인터넷 액세스를 Azure 센티넬에 연결

> [!IMPORTANT]
> Azure Sentinel의 Zscaler 데이터 커넥터는 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Zscaler 인터넷 액세스 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. Zscaler 데이터 커넥터를 사용하면 Zia(Zscaler) 로그를 Azure Sentinel과 쉽게 연결하여 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 Zscaler를 사용하면 조직의 인터넷 사용에 대한 더 많은 정보를 제공하고 보안 작업 기능을 향상시킵니다. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>CEF 메시지를 보내도록 Zscaler 구성

1. Zscaler 어플라이언스에서 는 어플라이언스가 Log Analytics 에이전트를 기반으로 필요한 형식으로 필요한 로그를 Azure Sentinel Syslog 에이전트로 보내도록 이러한 값을 설정해야 합니다. Azure Sentinel 에이전트의 Syslog 데몬에서도 이러한 매개 변수를 수정하는 한 어플라이언스에서 이러한 매개 변수를 수정할 수 있습니다.
    - 프로토콜 = TCP
    - 포트 = 514
    - 형식 = CEF
    - IP 주소 - CEF 메시지를 이 목적을 위해 전용한 가상 시스템의 IP 주소로 보내야 합니다.
 자세한 내용은 [Zscaler 및 Azure Sentinel 배포 가이드를](https://aka.ms/ZscalerCEFInstructions)참조하십시오.
 
   > [!NOTE]
   > 이 솔루션은 Syslog RFC 3164 또는 RFC 5424를 지원합니다.


1. CEF 이벤트에 대한 로그 분석에서 관련 스키마를 `CommonSecurityLog`사용하려면 을 검색합니다.
1. [3단계 계속: 연결 유효성 검사](connect-cef-verify.md).


## <a name="next-steps"></a>다음 단계
이 문서에서는 Zscaler 인터넷 액세스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


