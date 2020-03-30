---
title: F5 데이터를 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: F5 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588266"
---
# <a name="connect-f5-to-azure-sentinel"></a>F5를 Azure 센티넬에 연결

이 문서에서는 F5 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. F5 데이터 커넥터를 사용하면 F5 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 F5를 사용하면 조직의 인터넷 사용에 대한 더 많은 정보를 얻을 수 있으며 보안 작업 기능이 향상됩니다. 

## <a name="configure-your-f5-to-send-cef-messages"></a>CEF 메시지를 보내도록 F5 구성

1. [F5 응용 프로그램 보안 이벤트 로깅으로](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)이동하여 다음 지침을 사용하여 원격 로깅을 설정하는 지침을 따릅니다.
   - 원격 **저장소 유형을** **CEF로**설정합니다.
   - **프로토콜을** **TCP로**설정합니다.
   - IP **주소를** Syslog 서버 IP 주소로 설정합니다.
   - 포트 **번호를** **514로**설정하거나 에이전트가 사용하도록 설정한 포트를 설정합니다.
   - 최대 쿼리 **문자열 크기를** 에이전트에서 설정한 크기로 설정할 수 있습니다.

1. CEF 이벤트에 대한 로그 분석에서 관련 스키마를 `CommonSecurityLog`사용하려면 을 검색합니다.

1. [3단계 계속: 연결 유효성 검사](connect-cef-verify.md).


## <a name="next-steps"></a>다음 단계
이 문서에서는 F5를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

