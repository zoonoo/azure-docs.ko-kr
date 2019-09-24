---
title: Check Point 데이터를 Azure 센티널에 연결 | Microsoft Docs
description: Check Point 데이터를 Azure 센티널에 연결 하는 방법을 알아봅니다.
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 1bfa59e92ce2cde45b448174260396f4e93a6282
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240128"
---
# <a name="connect-your-check-point-appliance"></a>Check Point 어플라이언스 연결



로그 파일을 Syslog CEF로 저장 하 여 Azure 센티널을 모든 Check Point 어플라이언스에 연결할 수 있습니다. Azure 센티널과 통합 하면 Check Point에서 로그 파일 데이터에 대 한 분석 및 쿼리를 쉽게 실행할 수 있습니다. Azure 센티널에서 CEF 데이터를 수집 하는 방법에 대 한 자세한 내용은 [cef 어플라이언스 연결](connect-common-event-format.md)을 참조 하세요.

> [!NOTE]
> 데이터는 Azure 센티널을 실행 하는 작업 영역의 지리적 위치에 저장 됩니다.

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>1단계: 에이전트를 사용 하 여 Check Point 어플라이언스 연결

Check Point 어플라이언스를 Azure 센티널에 연결 하려면 어플라이언스와 Azure 센티널 간의 통신을 지원 하기 위해 전용 컴퓨터 (VM 또는 온-프레미스)에 에이전트를 배포 해야 합니다. 

또는 에이전트를 기존 Azure VM, 다른 클라우드의 VM 또는 온-프레미스 컴퓨터에서 수동으로 배포할 수 있습니다.

> [!NOTE]
> 조직의 보안 정책에 따라 컴퓨터의 보안을 구성 해야 합니다. 예를 들어 회사 네트워크 보안 정책에 맞게 네트워크를 구성 하 고, 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다. 

두 옵션의 네트워크 다이어그램을 보려면 [연결 데이터 원본](connect-data-sources.md)을 참조 하세요.

### <a name="deploy-the-agent"></a>에이전트 배포
1. Azure 센티널 포털에서 **데이터 커넥터** 를 클릭 하 고 **확인 지점** 을 선택한 다음 **커넥터 페이지를 엽니다**. 

1. **Syslog 에이전트 다운로드 및 설치**에서 컴퓨터 유형 (Azure 또는 온-프레미스)을 선택 합니다. 
1. 열리는 **가상 컴퓨터** 화면에서 사용 하려는 컴퓨터를 선택 하 고 **연결**을 클릭 합니다.
1. **Azure Linux virtual machines 용 에이전트 다운로드 및 설치**를 선택 하는 경우 컴퓨터를 선택 하 고 **연결**을 클릭 합니다. **비 Azure linux 가상 머신에 대 한 에이전트 다운로드 및 설치**를 선택한 경우 **직접 에이전트** 화면에서 **Linux 용 에이전트 다운로드 및**등록 아래에 있는 스크립트를 실행 합니다.
 
1. 커넥터 화면의 **Syslog 구성 및 전달**에서 syslog 데몬이 **rsyslog. d** 또는 **syslog**를 설정 하는지 여부를 설정 합니다. 
1. 다음 명령을 복사 하 여 어플라이언스에서 실행 합니다.
     - Rsyslog. d를 선택한 경우:
            
       1. 기능 local_4 및 "Check Point"를 수신 하 고 포트 25226을 사용 하 여 Syslog 메시지를 Azure 센티널 에이전트로 보내도록 Syslog 디먼에 지시 합니다. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
           
       2. 25226 포트에서 수신 하도록 Syslog 에이전트를 구성 하는 [security_events config 파일](https://aka.ms/asi-syslog-config-file-linux) 을 다운로드 하 여 설치 합니다. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`여기서 {0} 는 작업 영역 GUID로 바꾸어야 합니다.
           
       1. Syslog 데몬을 다시 시작 합니다.`sudo service rsyslog restart`
             
    - Syslog를 선택한 경우:

         1. 기능 local_4 및 "Check Point"를 수신 하 고 포트 25226을 사용 하 여 Syslog 메시지를 Azure 센티널 에이전트로 보내도록 Syslog 디먼에 지시 합니다. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
         2. 25226 포트에서 수신 하도록 Syslog 에이전트를 구성 하는 [security_events config 파일](https://aka.ms/asi-syslog-config-file-linux) 을 다운로드 하 여 설치 합니다. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`여기서 {0} 는 작업 영역 GUID로 바꾸어야 합니다.

        3. Syslog 데몬을 다시 시작 합니다.`sudo service syslog-ng restart`
2. 다음 명령을 사용 하 여 Syslog 에이전트를 다시 시작 합니다.`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. 다음 명령을 실행 하 여 에이전트 로그에 오류가 없는지 확인 합니다.`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>2단계: Check Point 로그를 Syslog 에이전트로 전달

Syslog 에이전트를 통해 Azure 작업 영역에 CEF 형식의 Syslog 메시지를 전달 하도록 Check Point 어플라이언스를 구성 합니다.

1. [Check Point 로그 내보내기](https://aka.ms/asi-syslog-checkpoint-forwarding)로 이동 합니다.
2. 아래 지침을 사용 하 여 **기본 배포** 까지 아래로 스크롤하고 지침에 따라 연결을 설정 합니다.
   - **Syslog 포트** 를 **514** 으로 설정 하거나 에이전트에서 설정한 포트를 설정 합니다.
     - CLI에서 **이름** 및 **대상 서버 Ip 주소** 를 SYSLOG 에이전트 이름 및 ip 주소로 바꿉니다.
     - 형식을 **Cef**로 설정 합니다.
3. R 77.30 또는 R 80.10 버전을 사용 하는 경우 **설치** 까지 스크롤한 다음 지침에 따라 해당 버전에 대 한 로그 내보내기를 설치 합니다.
 
## <a name="step-3-validate-connectivity"></a>3단계: 연결 유효성 검사

로그가 Log Analytics 표시 되기 시작할 때까지 20 분 정도 걸릴 수 있습니다. 

1. 올바른 기능을 사용 해야 합니다. 이 기능은 어플라이언스와 Azure 센티널에서 동일 해야 합니다. Azure 센티널에서 사용 중인 기능 파일을 확인 하 고 파일 `security-config-omsagent.conf`에서 수정할 수 있습니다. 

2. 로그가 Syslog 에이전트의 올바른 포트에 도달 하는지 확인 합니다. Syslog 에이전트 컴퓨터에서 다음 명령을 실행 합니다. `tcpdump -A -ni any  port 514 -vv`이 명령은 장치에서 Syslog 컴퓨터로 스트리밍하는 로그를 보여 줍니다. 올바른 포트 및 올바른 기능의 원본 어플라이언스에서 로그를 수신 하 고 있는지 확인 합니다.

3. 전송 하는 로그가 [RFC 3164](https://tools.ietf.org/html/rfc3164)을 준수 하는지 확인 합니다.

4. Syslog 에이전트를 실행 하는 컴퓨터에서 명령을 `netstat -a -n:`사용 하 여 이러한 포트 514, 25226이 열려 있고 수신 대기 하는지 확인 합니다. 이 명령을 사용 하는 방법에 대 한 자세한 내용은 [netstat (8)-Linux 매뉴얼 페이지](https://linux.die.net/man/8/netstat)를 참조 하세요. 제대로 수신 대기 하는 경우 다음이 표시 됩니다.

   ![Azure 센티널 포트](./media/connect-cef/ports.png) 

5. 데몬이 로그를 전송 하는 포트 514에서 수신 대기 하도록 설정 되어 있는지 확인 합니다.
    - Rsyslog의 경우:<br>파일 `/etc/rsyslog.conf` 에 다음 구성이 포함 되어 있는지 확인 합니다.

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      자세한 내용은 [imudp: UDP Syslog 입력 모듈](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) 및 [imtcp: TCP Syslog 입력 모듈](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Syslog 기능:<br>파일 `/etc/syslog-ng/syslog-ng.conf` 에 다음 구성이 포함 되어 있는지 확인 합니다.

           # source s_network {
            network( transport(UDP) port(514));
             };
    자세한 내용은 [syslog 기능 오픈 소스 버전 3.16-관리 가이드](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)를 참조 하세요.

1. Syslog 디먼 및 에이전트 간에 통신이 있는지 확인 합니다. Syslog 에이전트 컴퓨터에서 다음 명령을 실행 합니다. `tcpdump -A -ni any  port 25226 -vv`이 명령은 장치에서 Syslog 컴퓨터로 스트리밍하는 로그를 보여 줍니다. 에이전트 에서도 로그를 수신 하 고 있는지 확인 합니다.

6. 두 명령 모두 성공 결과를 제공한 경우에는 Log Analytics를 확인 하 여 로그가 도착 했는지 확인 합니다. 이러한 어플라이언스에서 스트리밍된 모든 이벤트는 형식 아래 `CommonSecurityLog` Log Analytics 원시 형식으로 표시 됩니다.

7. 오류가 있는지 또는 로그가 도착 하지 않았는지 확인 하려면에서 `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`찾습니다. 로그 형식 불일치 오류가 있는 경우로 `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 이동 하 여 파일 `security_events.conf`을 확인 하 고이 파일에 표시 되는 regex 형식과 일치 하는 로그를 확인 합니다.

8. Syslog 메시지의 기본 크기는 2048 바이트 (2kb)로 제한 되어야 합니다. 로그가 너무 길면 다음 명령을 사용 하 여 security_events를 업데이트 합니다.`message_length_limit 4096`

4. 다음 명령을 실행 해야 합니다.
  
   - Syslog를 사용 하는 경우 다음 명령을 실행 합니다 (Syslog 에이전트가 다시 시작 됨).

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        Syslog 디먼을 다시 시작 합니다.`sudo service syslog-ng restart`

   - Rsyslog를 사용 하는 경우 다음 명령을 실행 합니다 (Syslog 에이전트가 다시 시작 됨). 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     Syslog 디먼을 다시 시작 합니다.`sudo service rsyslog restart`




## <a name="next-steps"></a>다음 단계
이 문서에서는 Check Point 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats-built-in.md)시작 합니다.

