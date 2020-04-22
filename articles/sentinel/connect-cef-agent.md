---
title: CEF 데이터를 Azure Sentinel에 연결하기 위해 로그 포워더 배포 | 마이크로 소프트 문서
description: CEF 데이터를 Azure Sentinel에 연결하기 위해 에이전트를 배포하는 방법을 알아봅니다.
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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731652"
---
# <a name="step-1-deploy-the-log-forwarder"></a>1단계: 로그 전달자 배포


이 단계에서는 보안 솔루션에서 Azure Sentinel 작업 영역으로 로그를 전달하는 Linux 컴퓨터를 지정하고 구성합니다. 이 컴퓨터는 온-프레미스 환경의 물리적 또는 가상 컴퓨터, Azure VM 또는 다른 클라우드의 VM일 수 있습니다. 제공된 링크를 사용하여 다음 작업을 수행하는 지정된 컴퓨터에서 스크립트를 실행합니다.
- Linux용 로그 분석 에이전트(OMS 에이전트라고도 함)를 설치하고 다음 을 위해 구성합니다.
    - TCP 포트 25226에 내장 된 리눅스 Syslog 데몬에서 CEF 메시지를 듣고
    - TLS를 통해 Azure Sentinel 작업 공간으로 안전하게 메시지를 전송하여 구문 분석 및 보강됩니다.

- 다음과 같은 목적을 위해 기본 제공 Linux Syslog 데몬(rsyslog.d/syslog-ng)을 구성합니다.
    - TCP 포트 514의 보안 솔루션에서 Syslog 메시지 수신 대기
    - TCP 포트 25226을 사용하여 로컬 호스트의 Log Analytics 에이전트에 CEF로 식별되는 메시지만 전달
 
## <a name="prerequisites"></a>사전 요구 사항

- 지정된 Linux 컴퓨터에서 높은 권한(sudo)이 있어야 합니다.
- 리눅스 컴퓨터에 파이썬이 설치되어 있어야합니다.<br>명령을 `python -version` 사용하여 확인합니다.
- Log Analytics 에이전트를 설치하기 전에 Linux 컴퓨터를 Azure 작업 영역에 연결해서는 안 됩니다.

## <a name="run-the-deployment-script"></a>배포 스크립트 실행
 
1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터를 클릭합니다.** 커넥터 목록에서 **CEF(일반 이벤트 형식)** 타일을 클릭한 다음 오른쪽 하단의 **연결선 열기 페이지** 단추를 클릭합니다. 

1. **1.2 에서 CEF 수집기를 Linux 컴퓨터에 설치하고**다음 스크립트 실행에서 제공된 링크를 **복사하여 CEF 컬렉터를 설치하고 적용하거나**아래 텍스트에서 복사합니다.

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. 스크립트가 실행되는 동안 오류 또는 경고 메시지가 없는지 확인합니다.

[2단계 계속: 보안 솔루션을 구성하여 CEF 메시지를 전달합니다.](connect-cef-solution-config.md)

## <a name="deployment-script-explained"></a>배포 스크립트 설명

다음은 배포 스크립트의 작업에 대한 명령별 설명입니다.

적절한 설명을 보려면 syslog 데몬을 선택합니다.

# <a name="rsyslog-daemon"></a>[rsyslog 데몬](#tab/rsyslog)

1. **로그 분석 에이전트 다운로드 및 설치:**

    - 로그 분석 (OMS) 리눅스 에이전트에 대 한 설치 스크립트를 다운로드<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 로그 분석 에이전트 설치<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **시슬로그 데몬 구성:**

    1. syslog 구성 파일을 `/etc/rsyslog.conf`사용하여 TCP 통신에 대한 포트 514를 엽니다.

    1. syslog 데몬 디렉토리에 특수 구성 파일을 `security-config-omsagent.conf` 삽입하여 TCP 포트 25226의 로그 분석 에이전트에 `/etc/rsyslog.d/`CEF 메시지를 전달하도록 데몬을 구성합니다.

        `security-config-omsagent.conf` 파일의 내용 :

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **시슬로그 데몬 다시 시작**

    `service rsyslog restart`

1. **포트 25226에서 수신 및 CEF 메시지를 Azure Sentinel으로 전달하도록 로그 분석 에이전트 구성 설정**

    1. 로그 분석 에이전트 GitHub 리포지토리에서 구성 다운로드<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 로그 분석 에이전트 다시 시작<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng 데몬](#tab/syslogng)

1. **로그 분석 에이전트 다운로드 및 설치:**

    - 로그 분석 (OMS) 리눅스 에이전트에 대 한 설치 스크립트를 다운로드<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 로그 분석 에이전트 설치<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **시슬로그 데몬 구성:**

    1. syslog 구성 파일을 `/etc/syslog-ng/syslog-ng.conf`사용하여 TCP 통신에 대한 포트 514를 엽니다.

    1. syslog 데몬 디렉토리에 특수 구성 파일을 `security-config-omsagent.conf` 삽입하여 TCP 포트 25226의 로그 분석 에이전트에 `/etc/syslog-ng/conf.d/`CEF 메시지를 전달하도록 데몬을 구성합니다.

        `security-config-omsagent.conf` 파일의 내용 :

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **시슬로그 데몬 다시 시작**

    `service syslog-ng restart`

1. **포트 25226에서 수신 및 CEF 메시지를 Azure Sentinel으로 전달하도록 로그 분석 에이전트 구성 설정**

    1. 로그 분석 에이전트 GitHub 리포지토리에서 구성 다운로드<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 로그 분석 에이전트 다시 시작<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하기 위해 Log Analytics 에이전트를 배포하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.

