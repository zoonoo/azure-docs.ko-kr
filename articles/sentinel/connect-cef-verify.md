---
title: Azure 센티넬에 대한 연결 유효성 검사 | 마이크로 소프트 문서
description: 보안 솔루션의 연결을 확인하여 CEF 메시지가 Azure Sentinel로 전달되는지 확인합니다.
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
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731827"
---
# <a name="step-3-validate-connectivity"></a>3단계: 연결 의 유효성 검사

로그 포워더(1단계)를 배포하고 CEF 메시지를 보내도록 보안 솔루션을 구성한 후(2단계에서) 다음 지침을 따라 보안 솔루션과 Azure Sentinel 간의 연결을 확인합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 로그 전달자 컴퓨터에서 높은 권한(sudo)이 있어야 합니다.

- 로그 포워더 컴퓨터에 파이썬이 설치되어 있어야 합니다.<br>
명령을 `python –version` 사용하여 확인합니다.

## <a name="how-to-validate-connectivity"></a>연결의 유효성을 검사하는 방법

1. Azure Sentinel 탐색 메뉴에서 **로그를 엽니다.** **CommonSecurityLog** 스키마를 사용하여 쿼리를 실행하여 보안 솔루션에서 로그를 받는지 확인합니다.<br>
로그가 **로그 분석에**표시되기 시작할 때까지 약 20분이 걸릴 수 있습니다. 

1. 쿼리 결과가 표시되지 않으면 보안 솔루션에서 이벤트가 생성되고 있는지 확인하거나 일부를 생성해 보고 지정한 Syslog 포워더 컴퓨터로 전달되고 있는지 확인합니다. 

1. 로그 전달자에서 다음 스크립트를 실행하여 보안 솔루션, 로그 전달자 및 Azure Sentinel 간의 연결을 확인합니다. 이 스크립트는 데몬이 올바른 포트에서 수신 대기 중이고, 전달이 제대로 구성되고 있는지, 데몬과 Log Analytics 에이전트 간의 통신을 차단하는 것이 없는지 확인합니다. 또한 종단 간 연결을 확인하기 위해 모의 메시지 'TestCommonEventFormat'을 보냅니다. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>유효성 검사 스크립트 설명

유효성 검사 스크립트는 다음 검사를 수행합니다.

# <a name="rsyslog-daemon"></a>[rsyslog 데몬](#tab/rsyslog)

1. 파일이<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    유효합니다.

1. 파일에 다음 텍스트가 포함되어 있습니까?

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. 네트워크 트래픽을 차단할 수 있는 보안 향상 기능(예: 호스트 방화벽)이 있는지 확인합니다.

1. syslog 데몬(rsyslog)이 CEF(정규식 사용)로 식별되는 메시지를 TCP 포트 25226의 로그 분석 에이전트로 보내도록 올바르게 구성되어 있는지 확인합니다.

    - 구성 파일:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. syslog 데몬이 포트 514에서 데이터를 수신하고 있는지 확인합니다.

1. 필요한 연결이 설정되어 있는지 확인: 데이터 수신을 위한 tcp 514, syslog 데몬과 로그 분석 에이전트 간의 내부 통신을 위한 tcp 25226

1. 로컬 호스트에서 PORT 514로 MOCK 데이터를 보냅니다. 이 데이터는 다음 쿼리를 실행하여 Azure Sentinel 작업 영역에서 관찰할 수 있어야 합니다.

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng 데몬](#tab/syslogng)

1. 파일이<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    유효합니다.

1. 파일에 다음 텍스트가 포함되어 있습니까?

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. 네트워크 트래픽을 차단할 수 있는 보안 향상 기능(예: 호스트 방화벽)이 있는지 확인합니다.

1. syslog 데몬(syslog-ng)이 CEF(정규식 사용)로 식별되는 메시지를 TCP 포트 25226의 로그 분석 에이전트로 보내도록 올바르게 구성되어 있는지 확인합니다.

    - 구성 파일:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. syslog 데몬이 포트 514에서 데이터를 수신하고 있는지 확인합니다.

1. 필요한 연결이 설정되어 있는지 확인: 데이터 수신을 위한 tcp 514, syslog 데몬과 로그 분석 에이전트 간의 내부 통신을 위한 tcp 25226

1. 로컬 호스트에서 PORT 514로 MOCK 데이터를 보냅니다. 이 데이터는 다음 쿼리를 실행하여 Azure Sentinel 작업 영역에서 관찰할 수 있어야 합니다.

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

