---
title: Check Point 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Check Point 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588419"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Check Point를 Azure 센티널에 연결



이 문서에서는 Check Point 어플라이언스를 Azure 센티널에 연결 하는 방법을 설명 합니다. Check Point 데이터 커넥터를 사용 하면 확인 지점 로그를 Azure 센티널에 쉽게 연결 하 고, 대시보드를 보고, 사용자 지정 경고를 만들고, 조사를 개선할 수 있습니다. Azure 센티널에서 Check Point를 사용 하면 조직의 인터넷 사용에 대 한 자세한 정보를 제공 하 고 보안 작업 기능을 향상 시킬 수 있습니다. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Check Point 로그를 Syslog 에이전트로 전달

Syslog 에이전트를 통해 Azure 작업 영역에 CEF 형식의 Syslog 메시지를 전달 하도록 Check Point 어플라이언스를 구성 합니다.

1. [Check Point 로그 내보내기](https://aka.ms/asi-syslog-checkpoint-forwarding)로 이동 합니다.
1. 아래 지침을 사용 하 여 **기본 배포** 까지 아래로 스크롤하고 지침에 따라 연결을 설정 합니다.
   - **Syslog 포트** 를 **514** 으로 설정 하거나 에이전트에서 설정한 포트를 설정 합니다.
     - CLI에서 **이름** 및 **대상 서버 Ip 주소** 를 SYSLOG 에이전트 이름 및 ip 주소로 바꿉니다.
     - 형식을 **Cef**로 설정 합니다.
1. R 77.30 또는 R 80.10 버전을 사용 하는 경우 **설치** 까지 스크롤한 다음 지침에 따라 해당 버전에 대 한 로그 내보내기를 설치 합니다.
1. [3 단계: 연결 유효성 검사](connect-cef-verify.md)로 계속 진행 합니다.
 

## <a name="next-steps"></a>다음 단계
이 문서에서는 Check Point 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [연결의 유효성을 검사](connect-cef-verify.md)합니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md) 하 여 데이터를 모니터링 합니다.


