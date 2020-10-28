---
title: Azure 센티널에 대 한 연결 유효성 검사 | Microsoft Docs
description: 보안 솔루션 연결의 유효성을 검사 하 여 CEF 메시지가 Azure 센티널로 전달 되는지 확인 합니다.
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: ba14e2c475611ed77661060d6e17ae0bcbf0a6ca
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744214"
---
# <a name="step-3-validate-connectivity"></a>3 단계: 연결 유효성 검사

로그 전달자 (1 단계)를 배포 하 고 보안 솔루션을 구성 하 여 (2 단계)이 지침에 따라 보안 솔루션과 Azure 센티널 간의 연결을 확인 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 로그 전달자 컴퓨터에 상승 된 권한 (sudo)이 있어야 합니다.

- 로그 전달자 컴퓨터에 **python 2.7** 이 설치 되어 있어야 합니다.<br>
명령을 사용 `python –version` 하 여 확인 합니다.

- 이 프로세스의 어느 시점에서 작업 영역 ID 및 작업 영역 기본 키가 필요할 수 있습니다. 작업 영역 리소스의 **에이전트 관리** 에서 찾을 수 있습니다.

## <a name="how-to-validate-connectivity"></a>연결의 유효성을 검사 하는 방법

1. Azure 센티널 탐색 메뉴에서 **로그** 를 엽니다. **CommonSecurityLog** 스키마를 사용 하 여 쿼리를 실행 하 여 보안 솔루션에서 로그를 받는지 확인 합니다.<br>
로그가 **Log Analytics** 표시 되기 시작할 때까지 약 20 분 정도 걸릴 수 있습니다. 

1. 쿼리에서 결과가 표시 되지 않는 경우 보안 솔루션에서 이벤트를 생성 하 고 있는지 확인 하거나, 일부를 생성 하 고, 지정한 Syslog 전달자 컴퓨터로 전달 되는지 확인 합니다. 

1. 로그 전달자 (자리 표시자 대신 작업 영역 ID 적용)에서 다음 스크립트를 실행 하 여 보안 솔루션, 로그 전달자 및 Azure 센티널 간의 연결을 확인 합니다. 이 스크립트는 데몬이 올바른 포트에서 수신 대기 중인지, 전달이 올바르게 구성 되었는지, 그리고 디먼과 Log Analytics 에이전트 간의 통신을 차단 하 고 있는지를 확인 합니다. 또한 모의 연결을 확인 하기 위해 모의 메시지 ' TestCommonEventFormat '를 보냅니다. <br>

    ```bash
    sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]` 
    ```

   - 명령을 실행 하 여 ***컴퓨터* 필드의 매핑에** 대 한 문제를 해결 하도록 지시 하는 메시지가 표시 될 수 있습니다. 자세한 내용은 [유효성 검사 스크립트의 설명을](#mapping-command) 참조 하세요.

    - **CISCO global.asa 방화벽 로그의 구문 분석과** 관련 된 문제를 해결 하는 명령을 실행 하는 메시지가 표시 될 수 있습니다. 자세한 내용은 [유효성 검사 스크립트의 설명을](#parsing-command) 참조 하세요.

## <a name="validation-script-explained"></a>유효성 검사 스크립트 설명

유효성 검사 스크립트는 다음 검사를 수행 합니다.

# <a name="rsyslog-daemon"></a>[rsyslog 디먼](#tab/rsyslog)

1. 파일이 있는지 확인 합니다.<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    가 있고가 올바릅니다.

1. 파일에 다음 텍스트가 포함 되어 있는지 확인 합니다.

    ```bash
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
    ```

1. 다음 명령을 사용 하 여 Cisco GLOBAL.ASA 방화벽 이벤트에 대 한 구문 분석이 예상 대로 구성 되었는지 확인 합니다. 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>구문 분석에 문제가 발생 하는 경우 스크립트는 **다음 명령을 수동으로 실행** 하는 오류 메시지를 생성 합니다. 자리 표시자 대신 작업 영역 ID를 적용 합니다. 이 명령은 올바른 구문 분석을 확인 하 고 에이전트를 다시 시작 합니다.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 다음 명령을 사용 하 여 syslog 원본의 *컴퓨터* 필드가 Log Analytics 에이전트에서 올바르게 매핑되는지 확인 합니다. 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>매핑에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하는 오류 메시지를 생성 합니다 (자리 표시자 대신 작업 영역 ID 적용). 이 명령은 올바른 매핑을 확인 하 고 에이전트를 다시 시작 합니다.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 네트워크 트래픽 (예: 호스트 방화벽)을 차단할 수 있는 보안 강화 기능이 컴퓨터에 있는지 확인 합니다.

1. Syslog 디먼 (rsyslog)이 TCP 포트 25226의 Log Analytics 에이전트에 메시지 (CEF로 식별 됨)를 보내도록 올바르게 구성 되어 있는지 확인 합니다.

    - 구성 파일: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Syslog 디먼 및 Log Analytics 에이전트를 다시 시작 합니다.

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 필요한 연결이 설정 되었는지 확인 합니다. tcp 514: 데이터를 수신 하 고, syslog 디먼과 Log Analytics 에이전트 간의 내부 통신에 tcp 25226를 설정 합니다.

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Syslog 데몬이 포트 514에서 데이터를 받고 있고 에이전트가 포트 25226에서 데이터를 수신 하 고 있는지 확인 합니다.

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. 호스트 \에서 모의 데이터를 514 포트에 보냅니다. 이 데이터는 다음 쿼리를 실행 하 여 Azure 센티널 작업 영역에서 관찰 가능 해야 합니다.

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-기능 데몬](#tab/syslogng)

1. 파일이 있는지 확인 합니다.<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    가 있고가 올바릅니다.

1. 파일에 다음 텍스트가 포함 되어 있는지 확인 합니다.

    ```bash
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
    ```

1. 다음 명령을 사용 하 여 Cisco GLOBAL.ASA 방화벽 이벤트에 대 한 구문 분석이 예상 대로 구성 되었는지 확인 합니다. 

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>구문 분석에 문제가 발생 하는 경우 스크립트는 **다음 명령을 수동으로 실행** 하는 오류 메시지를 생성 합니다. 자리 표시자 대신 작업 영역 ID를 적용 합니다. 이 명령은 올바른 구문 분석을 확인 하 고 에이전트를 다시 시작 합니다.
    
        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 다음 명령을 사용 하 여 syslog 원본의 *컴퓨터* 필드가 Log Analytics 에이전트에서 올바르게 매핑되는지 확인 합니다. 

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>매핑에 문제가 있는 경우 스크립트는 **다음 명령을 수동으로 실행** 하는 오류 메시지를 생성 합니다 (자리 표시자 대신 작업 영역 ID 적용). 이 명령은 올바른 매핑을 확인 하 고 에이전트를 다시 시작 합니다.

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 네트워크 트래픽 (예: 호스트 방화벽)을 차단할 수 있는 보안 강화 기능이 컴퓨터에 있는지 확인 합니다.

1. Syslog 데몬 (syslog 사용)이 TCP 포트 25226의 Log Analytics 에이전트에 대 한 CEF (regex 사용)로 식별 되는 메시지를 보내도록 올바르게 구성 되어 있는지 확인 합니다.

    - 구성 파일: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Syslog 디먼 및 Log Analytics 에이전트를 다시 시작 합니다.

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 필요한 연결이 설정 되었는지 확인 합니다. tcp 514: 데이터를 수신 하 고, syslog 디먼과 Log Analytics 에이전트 간의 내부 통신에 tcp 25226를 설정 합니다.

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Syslog 데몬이 포트 514에서 데이터를 받고 있고 에이전트가 포트 25226에서 데이터를 수신 하 고 있는지 확인 합니다.

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. 호스트 \에서 모의 데이터를 514 포트에 보냅니다. 이 데이터는 다음 쿼리를 실행 하 여 Azure 센티널 작업 영역에서 관찰 가능 해야 합니다.

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>다음 단계
이 문서에서는 CEF 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.

