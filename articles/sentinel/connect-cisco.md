---
title: Azure 미리 보기 Sentinel Cisco 데이터 연결할 | Microsoft Docs
description: Cisco 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/19/2019
ms.author: rkarlin
ms.openlocfilehash: fd04e380b0b5732db1e39ffc9b5aa6047c7ef777
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382996"
---
# <a name="connect-your-cisco-asa-appliance"></a>Cisco ASA 어플라이언스에서 연결 

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel 모든 Cisco ASA 어플라이언스에 연결할 수 있습니다. Cisco ASA는 기본적으로 통합 된 Azure Sentinel 데이터 수집에 대 한 Cisco 어플라이언스에서 CEF로 로그를 저장 하지 않습니다, 경우에 Azure Sentinel 수집 하 여 해당 CEF 로그를 처리 하는 동일한 방식에 있도록 합니다. Azure Sentinel와 통합을 사용 하면 쉽게 분석 및 실행 쿼리 로그 파일 데이터에서 Cisco ASA에서 수 있습니다. 

> [!NOTE]
> 데이터 작업 영역의 Azure Sentinel 실행 하는 지리적 위치에 저장 됩니다.

## <a name="step-1-connect-your-cisco-asa-appliance-using-an-agent"></a>1단계: 에이전트를 사용 하 여 Cisco ASA 어플라이언스에서 연결

전용된 컴퓨터에 에이전트를 배포 해야 Azure Sentinel Cisco ASA 어플라이언스에서 연결 하려면 (VM 또는 온-프레미스) 어플라이언스 및 Azure Sentinel 간의 통신을 지원 하도록 합니다. 자동 또는 수동으로 에이전트를 배포할 수 있습니다. 자동 배포는 전용 컴퓨터가 Azure에서 만드는 새 VM인 경우에만 사용할 수 있습니다. 

또는 에이전트를 기존 Azure VM, 다른 클라우드의 VM 또는 온-프레미스 컴퓨터에서 수동으로 배포할 수 있습니다.

두 옵션의 네트워크 다이어그램을 참조 하세요 [데이터 원본 연결](connect-data-sources.md)합니다.

### <a name="deploy-the-agent-in-azure"></a>Azure에서 에이전트 배포

1. Sentinel Azure 포털에서 클릭 **데이터 커넥터** 어플라이언스 유형을 선택 하 고 있습니다. 

1. 아래 **Linux Syslog 에이전트 구성을**:
   - 선택할 **자동 배포** 위에서 설명한 대로 Azure Sentinel 에이전트를 사용 하 여 미리 설치 되어 있고 구성 필요한 모든 포함 하 여 새 컴퓨터를 만들려는 경우입니다. 선택 **자동 배포** 누릅니다 **자동 에이전트 배포**합니다. 이 작업 영역에 자동으로 연결 되는 전용된 VM에 대 한 구매 페이지로 이동 합니다. VM이를 **표준 D2s v3 (2 개 Vcpu, 8GB 메모리)** 있고 공용 IP 주소입니다.
      1. 에 **사용자 지정 배포** 페이지에서 세부 정보 제공 및 사용자 이름 및 암호를 선택 하 고 사용 약관에 동의 하는 경우 VM을 구입 합니다.
      1. 연결 페이지에서 나열 된 설정을 사용 하 여 로그를 전송 하기 위해 어플라이언스를 구성 합니다. 제네릭 일반적인 이벤트 형식 커넥터에 대해 이러한 설정을 사용 합니다.
         - Protocol = UDP
         - Port = 514
         - 기능 = 로컬 4
         - Format = CEF
   - 선택할 **수동 배포** Sentinel Azure 에이전트를 설치 해야는 전용 Linux 컴퓨터로 기존 VM을 사용 하려는 경우. 
      1. 아래 **Syslog 에이전트 다운로드 및 설치**를 선택 **Azure Linux 가상 머신에**합니다. 
      1. 에 **Virtual machines** 사용 하 고 클릭 하려는 컴퓨터를 선택 하는 화면이 열리면 **Connect**.
      1. 커넥터 화면에서 아래 **구성 및 정방향 Syslog**설정에 Syslog 디먼 인지 **rsyslog.d** 또는 **syslog ng**합니다. 
      1. 다음이 명령을 복사 하 고 어플라이언스에서 실행:
          - Rsyslog.d 선택한 경우:
              
            1. Syslog 디먼을 시설 local_4에서 수신 대기할 포트 25226으로 사용 하 여 Azure Sentinel 에이전트 Syslog 메시지를 보내는 알려 줍니다. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. 다운로드 및 설치 합니다 [security_events config 파일](https://aka.ms/asi-syslog-config-file-linux) 포트 25226으로에서 수신 대기할 Syslog 에이전트를 구성 하는 합니다. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 여기서 {0} 작업 영역의 GUID로 바꿔야 합니다.
            
            1. Syslog 디먼을 다시 시작 `sudo service rsyslog restart`
             
          - Ng syslog를 선택한 경우:

              1. Syslog 디먼을 시설 local_4에서 수신 대기할 포트 25226으로 사용 하 여 Azure Sentinel 에이전트 Syslog 메시지를 보내는 알려 줍니다. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. 다운로드 및 설치 합니다 [security_events config 파일](https://aka.ms/asi-syslog-config-file-linux) 포트 25226으로에서 수신 대기할 Syslog 에이전트를 구성 하는 합니다. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 여기서 {0} 작업 영역의 GUID로 바꿔야 합니다.

              3. Syslog 디먼을 다시 시작 `sudo service syslog-ng restart`
      2. 이 명령을 사용 하 여 Syslog 에이전트를 다시 시작 합니다. `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 이 명령을 실행 하 여 에이전트 로그에 오류가 있는지 확인 합니다. `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>온-프레미스 Linux 서버에서 에이전트 배포

Azure를 사용 하지 않는 경우 전용된 Linux 서버에서 실행 되도록 Azure Sentinel 에이전트를 수동으로 배포 합니다.


1. Sentinel Azure 포털에서 클릭 **데이터 커넥터** 어플라이언스 유형을 선택 하 고 있습니다.
1. 아래에 있는 전용된 Linux VM을 만들려면 **Linux Syslog 에이전트 구성을** 선택 **수동 배포**합니다.
   1. 아래 **Syslog 에이전트 다운로드 및 설치**를 선택 **비 Azure Linux 머신**합니다. 
   1. 에 **직접 에이전트** 열립니다를 선택 하는 화면 **Agent for Linux** 에이전트를 다운로드 하거나 Linux 컴퓨터에 다운로드 하려면이 명령을 실행 하려면:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. 커넥터 화면에서 아래 **구성 및 정방향 Syslog**설정에 Syslog 디먼 인지 **rsyslog.d** 또는 **syslog ng**합니다. 
      1. 다음이 명령을 복사 하 고 어플라이언스에서 실행:
         - Rsyslog를 선택한 경우:
           1. Syslog 디먼을 시설 local_4에서 수신 대기할 포트 25226으로 사용 하 여 Azure Sentinel 에이전트 Syslog 메시지를 보내는 알려 줍니다. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. 다운로드 및 설치 합니다 [security_events config 파일](https://aka.ms/asi-syslog-config-file-linux) 포트 25226으로에서 수신 대기할 Syslog 에이전트를 구성 하는 합니다. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 여기서 {0} 작업 영역의 GUID로 바꿔야 합니다.
           3. Syslog 디먼을 다시 시작 `sudo service rsyslog restart`
         - Ng syslog를 선택한 경우:
            1. Syslog 디먼을 시설 local_4에서 수신 대기할 포트 25226으로 사용 하 여 Azure Sentinel 에이전트 Syslog 메시지를 보내는 알려 줍니다. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. 다운로드 및 설치 합니다 [security_events config 파일](https://aka.ms/asi-syslog-config-file-linux) 포트 25226으로에서 수신 대기할 Syslog 에이전트를 구성 하는 합니다. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 여기서 {0} 작업 영역의 GUID로 바꿔야 합니다.
            3. Syslog 디먼을 다시 시작 `sudo service syslog-ng restart`
      1. 이 명령을 사용 하 여 Syslog 에이전트를 다시 시작 합니다. `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 이 명령을 실행 하 여 에이전트 로그에 오류가 있는지 확인 합니다. `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>2단계: Syslog 에이전트에 Cisco ASA 로그 전달

Cisco ASA Syslog 및 Azure Sentinel 에이전트 알고 CEF 로그 것 처럼 구문 분석 하는 방법으로 로그는 보내도록 CEF를 지원 하지 않습니다. Syslog 에이전트를 통해 Azure 작업 영역에 대 한 Syslog 메시지를 전달 하는 Cisco ASA를 구성 합니다.

로 이동 [외부 Syslog 서버로 전송 된 Syslog 메시지](https://aka.ms/asi-syslog-cisco-forwarding), 연결을 설정 하는 지침을 따릅니다. 메시지가 표시 되 면 이러한 매개 변수를 사용 합니다.
- 설정할 **포트** 514 또는 에이전트에서 설정 된 포트입니다.
- 설정할 **syslog_ip** 에이전트의 IP 주소입니다.
- 설정할 **로깅 기능** 에이전트에서 설정한 시설에 있습니다. 기본적으로 에이전트는 시설 4로 설정합니다.

Log Analytics에서 관련 스키마를 사용 하 여 Cisco 이벤트를 검색 `CommonSecurityLog`합니다.

## <a name="step-3-validate-connectivity"></a>3단계: 연결 유효성 검사

수준도 로그를 Log Analytics에 나타나기 시작 될 때까지 20 분 정도 걸릴 수 있습니다. 

1. 올바른 기능을 사용 해야 합니다. 시설 및 Azure Sentinel 어플라이언스에서에서 동일 해야 합니다. Azure Sentinel에서 사용 중이 고 파일의 수정 기능 파일을 확인할 수 있습니다 `security-config-omsagent.conf`합니다. 

2. 오른쪽 포트 Syslog 에이전트에 로그를 가져오고 있는지 확인 합니다. Syslog 에이전트 컴퓨터에서이 명령을 실행 합니다. `tcpdump -A -ni any  port 514 -vv` 이 명령은 Syslog 컴퓨터에 장치에서 스트림 로그를 보여 줍니다. 로그 원본 어플라이언스 오른쪽 시설에 올바른 포트에서에서 수신 되는 있는지 확인 합니다.

3. 보낼 로그를 준수 하는지 확인 [RFC 5424](https://tools.ietf.org/html/rfc542)합니다.

4. 컴퓨터의 Syslog 에이전트를 실행 해야 이러한 포트 514, 25226 열고 명령을 사용 하 여 수신 `netstat -a -n:`합니다. 이 명령을 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [netstat(8)-Linux 매뉴얼 페이지](https://linux.die.net/man/8/netstat)합니다. 제대로 수신 중인 경우이 표시 됩니다.

   ![Azure Sentinel 포트](./media/connect-cef/ports.png) 

5. 로그를 전송 하는 포트 514에서 수신 하도록 디먼을 설정 되어 있는지 확인 합니다.
    - Rsyslog의 경우:<br>확인 파일 `/etc/rsyslog.conf` 이 구성을 포함 합니다.

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      자세한 내용은 참조 하세요. [imudp: UDP Syslog 입력 모듈](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) 고 [imtcp: TCP Syslog 입력 모듈](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Syslog-ng:<br>확인 파일 `/etc/syslog-ng/syslog-ng.conf` 이 구성을 포함 합니다.

           # source s_network {
            network( transport(UDP) port(514));
             };
     자세한 내용은 참조 하세요. [imudp: UDP Syslog 입력 모듈] (자세한 내용은 참조는 [syslog ng 오픈 소스 버전 3.16-관리 가이드](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)합니다.

1. Syslog 디먼 및 에이전트 간의 통신 인지 확인 합니다. Syslog 에이전트 컴퓨터에서이 명령을 실행 합니다. `tcpdump -A -ni any  port 25226 -vv` 이 명령은 Syslog 컴퓨터에 장치에서 스트림 로그를 보여 줍니다. 에이전트에서 로그도 수신 되는 있는지 확인 합니다.

6. 성공적인 결과 제공 하는 모두 해당 명령의 경우에 Log Analytics 로그 도착 하는 경우 참조를 확인 합니다. 이러한 어플라이언스에서 스트리밍되는 모든 이벤트에서 Log Analytics에서 원시 형태로 나타나는 `CommonSecurityLog` 형식입니다.

7. 오류가 있는지 확인 하거나 로그 도착 하지 하는 경우 확인 `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`합니다. 로그 형식 불일치 오류가 있는 경우으로 이동 `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 파일 및 `security_events.conf`로그와이 파일에 나오는 regex 형식 일치 하는지 확인 합니다.

8. 에 Syslog 메시지 기본 크기는 2048 바이트 (2KB)로 제한 해야 합니다. 로그에 너무 긴 경우에이 명령을 사용 하 여 security_events.conf 업데이트: `message_length_limit 4096`




## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel Cisco ASA 어플라이언스에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

