---
title: Azure 센티널 Preview에 CEF 데이터 연결 | Microsoft Docs
description: Azure 센티널에 CEF 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0fbdba5c3fbfdfab5267407ccec9c611d74a5e02
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463985"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>일반적인 이벤트 형식을 사용 하 여 외부 솔루션 연결



이 문서에서는 Syslog 위에 CEF (일반 이벤트 형식) 메시지를 보내는 외부 보안 솔루션과 Azure 센티널을 연결 하는 방법을 설명 합니다. 

> [!NOTE] 
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="how-it-works"></a>작동 방법

어플라이언스와 Azure 센티널 간의 통신을 지원 하려면 전용 Linux 컴퓨터 (VM 또는 온-프레미스)에 에이전트를 배포 해야 합니다. 다음 다이어그램은 Azure에서 Linux VM의 경우의 설정에 대해 설명 합니다.

 ![Azure의 CEF](./media/connect-cef/cef-syslog-azure.png)

또는 다른 클라우드 또는 온-프레미스 컴퓨터에서 VM을 사용 하는 경우이 설정이 존재 합니다. 

 ![온-프레미스의 CEF](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>보안 고려 사항

조직의 보안 정책에 따라 컴퓨터의 보안을 구성 해야 합니다. 예를 들어 회사 네트워크 보안 정책에 맞게 네트워크를 구성 하 고, 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다. 다음 지침을 사용 하 여 컴퓨터 보안 구성을 향상 시킬 수 있습니다.  [Azure에서 VM 보안](../virtual-machines/linux/security-policy.md), [네트워크 보안에 대 한 모범 사례](../security/fundamentals/network-best-practices.md)를 참조 하세요.

보안 솔루션과 Syslog 컴퓨터 사이에 TLS 통신을 사용 하려면 tls: s s o l e n t e r s e n t e r s e n t e [r s-rsyslog를 사용 하 여 Syslog 트래픽 암호화](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [tls를 사용 하 여 로그 메시지 암호화 등에서 통신 하도록 syslog 디먼 (rsyslog 또는 syslog syslog](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>선행 조건
프록시로 사용 하는 Linux 컴퓨터가 다음 운영 체제 중 하나를 실행 하 고 있는지 확인 합니다.

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
 
 - 디먼 버전
   - Syslog-기능: 2.1-3.22.1
   - Rsyslog: v8
  
 - Syslog Rfc 지원
   - Syslog RFC 3164
   - Syslog RFC 5424
 
컴퓨터가 다음 요구 사항도 충족 하는지 확인 합니다. 
- 사용 권한
    - 컴퓨터에 상승 된 권한 (sudo)이 있어야 합니다. 
- 소프트웨어 요구 사항
    - 컴퓨터에서 Python을 실행 하 고 있는지 확인 합니다.
## <a name="step-1-deploy-the-agent"></a>1 단계: 에이전트 배포

이 단계에서는 Azure 센티널과 보안 솔루션 간의 프록시로 사용할 Linux 컴퓨터를 선택 해야 합니다. 프록시 컴퓨터에서 다음을 수행 하는 스크립트를 실행 해야 합니다.
- Log Analytics 에이전트를 설치 하 고 Syslog 메시지를 수신 하는 데 필요한 대로 구성 합니다.
- Tcp 포트 514를 사용 하 여 Syslog 메시지를 수신 하 고, TCP 포트 25226을 사용 하 여 CEF 메시지만 Log Analytics 에이전트로 전달 하도록 Syslog 데몬을 구성 합니다.
- 는 데이터를 수집 하 여 Azure 센티널로 안전 하 게 전송 하도록 Syslog 에이전트를 설정 하 고,이를 구문 분석 하 고 보강 합니다.
 
 
1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **Cef (일반 이벤트 형식)** 를 선택한 다음 **커넥터 페이지를 엽니다**. 

1. **Syslog 에이전트 설치 및 구성**에서 컴퓨터 유형 (Azure, 다른 클라우드 또는 온-프레미스)을 선택 합니다. 
   > [!NOTE]
   > 다음 단계의 스크립트는 Log Analytics 에이전트를 설치 하 고 컴퓨터를 Azure 센티널 작업 영역에 연결 하기 때문에이 컴퓨터가 다른 작업 영역에 연결 되어 있지 않은지 확인 합니다.
1. 컴퓨터에 상승 된 권한 (sudo)이 있어야 합니다. 다음 명령을 사용 하 여 컴퓨터에 Python이 있는지 확인 합니다. `python –version`

1. 프록시 컴퓨터에서 다음 스크립트를 실행 합니다.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. 스크립트를 실행 하는 동안 오류 또는 경고 메시지가 표시 되지 않는지 확인 합니다.


## <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>2 단계: CEF 메시지를 보내도록 보안 솔루션 구성

1. 어플라이언스에서 Log Analytics 에이전트에 따라 어플라이언스에서 필요한 형식으로 필요한 로그를 Azure 센티널 Syslog 에이전트로 보내도록 이러한 값을 설정 해야 합니다. 이러한 매개 변수는 Azure 센티널 에이전트의 Syslog 디먼 에서도 수정할 수 있는 경우 어플라이언스에서 수정할 수 있습니다.
    - 프로토콜 = TCP
    - Port = 514
    - 형식 = CEF
    - IP 주소-이 목적을 위해 전용으로 사용 하는 가상 컴퓨터의 IP 주소에 CEF 메시지를 전송 해야 합니다.

   > [!NOTE]
   > 이 솔루션은 Syslog RFC 3164 또는 RFC 5424을 지원 합니다.


1. CEF 이벤트의 Log Analytics에서 관련 스키마를 사용 하려면 `CommonSecurityLog`를 검색 합니다.

## <a name="step-3-validate-connectivity"></a>3 단계: 연결 유효성 검사

1. Log Analytics를 열어 CommonSecurityLog 스키마를 사용 하 여 로그를 받았는지 확인 합니다.<br> 로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 

1. 스크립트를 실행 하기 전에 보안 솔루션에서 메시지를 전송 하 여 구성한 Syslog 프록시 컴퓨터로 전달 하는 것이 좋습니다. 
1. 컴퓨터에 상승 된 권한 (sudo)이 있어야 합니다. 다음 명령을 사용 하 여 컴퓨터에 Python이 있는지 확인 합니다. `python –version`
1. 다음 스크립트를 실행 하 여 에이전트, Azure 센티널 및 보안 솔루션 간의 연결을 확인 합니다. 디먼 전달이 제대로 구성 되어 있고, 올바른 포트에서 수신 대기 하 고, 디먼과 Log Analytics 에이전트 간의 통신을 차단 하 고 있지 않은지 확인 합니다. 또한이 스크립트는 모의 연결을 확인 하기 위해 모의 메시지 ' TestCommonEventFormat '를 보냅니다. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`


## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

