---
title: 체크 포인트 데이터를 Azure Sentinel에 연결| 마이크로 소프트 문서
description: 체크 포인트 데이터를 Azure Sentinel에 연결하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588419"
---
# <a name="connect-check-point-to-azure-sentinel"></a>체크 포인트와 Azure 센티넬 연결



이 문서에서는 Check Point 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. 체크 포인트 데이터 커넥터를 사용하면 검사점 로그를 Azure Sentinel에 쉽게 연결하고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure Sentinel에서 체크 포인트를 사용하면 조직의 인터넷 사용에 대한 더 많은 정보를 얻을 수 있으며 보안 작업 기능이 향상됩니다. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Syslog 에이전트에 대한 체크 포인트 로그 전달

CeF 형식의 Syslog 메시지를 Syslog 에이전트를 통해 Azure 작업 영역으로 전달하도록 Check Point 어플라이언스를 구성합니다.

1. 포인트 [로그 내보내기 로](https://aka.ms/asi-syslog-checkpoint-forwarding)이동합니다.
1. **기본 배포로** 스크롤하고 다음 지침을 사용하여 연결을 설정하려면 지침을 따릅니다.
   - **Syslog 포트를** **514** 또는 에이전트에 설정한 포트로 설정합니다.
     - CLI의 **이름** 및 **대상 서버 IP 주소를** Syslog 에이전트 이름 및 IP 주소로 바꿉니다.
     - 형식을 **CEF로**설정합니다.
1. 버전 R77.30 또는 R80.10을 사용하는 경우 **설치로** 스크롤하여 지침에 따라 버전에 대한 로그 내보내기를 설치합니다.
1. [3단계 계속: 연결 유효성 검사](connect-cef-verify.md).
 

## <a name="next-steps"></a>다음 단계
이 문서에서는 검사점 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [연결 의 유효성을 검사합니다.](connect-cef-verify.md)
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.


