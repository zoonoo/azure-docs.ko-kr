---
title: CEF 데이터를 Azure 센티넬 미리 보기에 연결 | 마이크로 소프트 문서
description: CEF 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: 8314614616c6b8969832d52fc684d47ba1bf0fe3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588351"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>공통 이벤트 형식을 사용하여 외부 솔루션 연결


CEF 메시지를 보내는 외부 솔루션을 연결하면 Azure Sentinel과 연결하는 세 단계가 있습니다.

1단계: 에이전트 2 단계를 [배포하여 CEF 연결:](connect-cef-agent.md) [솔루션별 단계](connect-cef-solution-config.md) 3: [연결 확인](connect-cef-verify.md)

이 문서에서는 연결의 작동 방식에 대해 설명하고, 전제 조건을 제공하며, Syslog 위에 CEF(공통 이벤트 형식) 메시지를 보내는 보안 솔루션에 에이전트를 배포하는 단계를 제공합니다. 

> [!NOTE] 
> 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.

이러한 연결을 만들려면 어플라이언스와 Azure Sentinel 간의 통신을 지원하기 위해 전용 Linux 컴퓨터(VM 또는 온프레미스)에 에이전트를 배포해야 합니다. 다음 다이어그램은 Azure에서 Linux VM이 발생할 경우의 설정에 대해 설명합니다.

 ![Azure의 CEF](./media/connect-cef/cef-syslog-azure.png)

또는 다른 클라우드에서 VM을 사용하거나 온-프레미스 컴퓨터에서 이 설정이 존재합니다. 

 ![온-프레미스의 CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>보안 고려 사항

조직의 보안 정책에 따라 컴퓨터의 보안을 구성해야 합니다. 예를 들어 회사 네트워크 보안 정책에 맞게 네트워크를 구성하고 데몬의 포트와 프로토콜을 변경하여 요구 사항에 맞게 변경할 수 있습니다. 다음 지침을 사용하여 컴퓨터 보안 구성을 개선할 수 있습니다:  [Azure의 보안 VM,](../virtual-machines/linux/security-policy.md) [네트워크 보안에 대한 모범 사례.](../security/fundamentals/network-best-practices.md)

보안 솔루션과 Syslog 컴퓨터 간의 TLS 통신을 사용하려면 TLS에서 통신하도록 Syslog 데몬(rsyslog 또는 syslog-ng)을 구성해야 합니다. [Encrypting Syslog Traffic with TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) [Encrypting log messages with TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

 
## <a name="prerequisites"></a>사전 요구 사항
프록시로 사용하는 Linux 컴퓨터가 다음 운영 체제 중 하나를 실행하고 있는지 확인합니다.

- 64비트
  - CentOS 6 및 7
  - Amazon Linux 2017.09
  - Oracle Linux 6 및 7
  - Red Hat Enterprise Linux Server 6 및 7
  - Debian GNU/Linux 8 및 9
  - Ubuntu Linux 14.04 LTS, 16.04 LTS 및 18.04 LTS
  - SUSE Linux Enterprise Server 12
- 32비트
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 및 9
   - Ubuntu Linux 14.04 LTS 및 16.04 LTS
 
 - 데몬 버전
   - Syslog-ng: 2.1 - 3.22.1
   - Rsyslog: v8
  
 - 시슬로그 RFC 지원
   - 시슬로그 RFC 3164
   - 시슬로그 RFC 5424
 
컴퓨터가 다음 요구 사항을 충족하는지 확인합니다. 
- 사용 권한
    - 컴퓨터에 높은 사용 권한(sudo)이 있어야 합니다. 
- 소프트웨어 요구 사항
    - 컴퓨터에서 파이썬이 실행되고 있는지 확인하십시오.



## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

