---
title: Azure 센티널에 F5 데이터 연결 | Microsoft Docs
description: Azure 센티널에 F5 데이터를 연결 하는 방법에 대해 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588266"
---
# <a name="connect-f5-to-azure-sentinel"></a>Azure 센티널에 F5 연결

이 문서에서는 Azure 센티널에 F5 어플라이언스를 연결 하는 방법을 설명 합니다. F5 데이터 커넥터를 사용 하면 F5 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure 센티널에서 F5 키를 사용 하면 조직의 인터넷 사용에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. 

## <a name="configure-your-f5-to-send-cef-messages"></a>CEF 메시지를 보내도록 F5 구성

1. [F5 응용 프로그램 보안 이벤트 로깅 구성](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)으로 이동 하 고 지침에 따라 다음 지침을 사용 하 여 원격 로깅을 설정 합니다.
   - **원격 저장소 유형을** **cef**로 설정 합니다.
   - **프로토콜** 을 **TCP**로 설정 합니다.
   - **Ip 주소** 를 SYSLOG 서버 ip 주소로 설정 합니다.
   - **포트 번호** 를 **514**로 설정 하거나 에이전트에서 사용할 포트를 설정 합니다.
   - **최대 쿼리 문자열 크기** 를 에이전트에서 설정한 크기로 설정할 수 있습니다.

1. CEF 이벤트의 Log Analytics에서 관련 스키마를 사용 하려면를 검색 `CommonSecurityLog`합니다.

1. [3 단계: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 F5 키를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.

