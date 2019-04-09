---
title: Azure Sentinel 미리 보기에서 CEF 데이터 수집 | Microsoft Docs
description: Azure Sentinel에 CEF 데이터를 수집 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 2117a139de52643f7cdbc6d054f46e5fb8ec0a77
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59005607"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>일반적인 이벤트 형식을 사용 하 여 외부 솔루션 연결

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Syslog 로그 파일을 저장할 수 있도록 외부 솔루션을 사용 하 여 Azure Sentinel에 연결할 수 있습니다. 어플라이언스를 사용 하면 로그와 Syslog 이벤트 CEF (일반적인 형식)를 저장할 수 있습니다, 하는 경우 Azure Sentinel와 통합을 사용 하면 쉽게 데이터 분석 및 쿼리를 실행할 수 있습니다.

> [!NOTE]
> 
> 데이터 작업 영역의 Azure Sentinel 실행 하는 지리적 위치에 저장 됩니다.

## <a name="how-it-works"></a>작동 방법

Azure Sentinel CEF 어플라이언스에서 사이의 연결에는 세 단계로 수행이 됩니다.

1. 어플라이언스에서 어플라이언스 Sentinel Syslog Azure 에이전트에 필요한 형식에서에 필요한 로그 전송 되도록 이러한 값을 설정 해야 합니다. Azure Sentinel 에이전트에서 Syslog 디먼에도 수정으로 기기에서 이러한 매개 변수를 수정할 수 있습니다.
    - Protocol = UDP
    - Port = 514
    - 기능 = 로컬 4
    - Format = CEF
2. Syslog 에이전트 데이터를 수집 및 안전 하 게 구문 분석 되 고 보강 한 Log Analytics로 보냅니다.
3. 에이전트는 분석, 상관 관계 규칙 및 대시보드를 사용 하 여 필요에 따라 쿼리할 수 있도록 Log Analytics 작업 영역에서 데이터를 저장 합니다.


## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>1단계: CEF 어플라이언스를 통해 전용된 Azure VM에 연결

전용된 Linux 컴퓨터에 에이전트를 배포 해야 (VM 또는 온-프레미스) 어플라이언스 및 Azure Sentinel 간의 통신을 지원 하도록 합니다. 자동 또는 수동으로 에이전트를 배포할 수 있습니다. 자동 배포 Resource Manager 템플릿을 기반으로 하 고 사용자 전용된 Linux 컴퓨터는 Azure에서 만드는 새 VM을 하는 경우에 사용할 수 있습니다.

 ![Azure의 CEF](./media/connect-cef/cef-syslog-azure.png)

또는 에이전트를 기존 Azure VM, 다른 클라우드의 VM 또는 온-프레미스 컴퓨터에서 수동으로 배포할 수 있습니다. 

 ![온-프레미스의 CEF](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Azure에서 에이전트 배포


1. Sentinel Azure 포털에서 클릭 **데이터 컬렉션** 어플라이언스 유형을 선택 하 고 있습니다. 

1. 아래 **Linux Syslog 에이전트 구성을**:
   - 선택할 **자동 배포** 위에서 설명한 대로 Azure Sentinel 에이전트를 사용 하 여 미리 설치 되어 있고 구성 필요한 모든 포함 하 여 새 컴퓨터를 만들려는 경우입니다. 선택 **자동 배포** 누릅니다 **자동 에이전트 배포**합니다. 이 작업 영역에 자동으로 연결 되는 전용된 Linux VM에 대 한 구매 페이지로 이동 합니다. VM이를 **표준 D2s v3 (2 개 vcpu, 8GB 메모리)** 있고 공용 IP 주소입니다.
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
            
            1. Syslog 디먼을 다시 시작 `sudo service rsyslog restart`<br> 자세한 내용은 참조는 [rsyslog 설명서](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
           
          - Ng syslog를 선택한 경우:

              1. Syslog 디먼을 시설 local_4에서 수신 대기할 포트 25226으로 사용 하 여 Azure Sentinel 에이전트 Syslog 메시지를 보내는 알려 줍니다. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. 다운로드 및 설치 합니다 [security_events config 파일](https://aka.ms/asi-syslog-config-file-linux) 포트 25226으로에서 수신 대기할 Syslog 에이전트를 구성 하는 합니다. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` 여기서 {0} 작업 영역의 GUID로 바꿔야 합니다.

              3. Syslog 디먼을 다시 시작 `sudo service syslog-ng restart` <br>자세한 내용은 참조는 [syslog ng 설명서](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)
      2. 이 명령을 사용 하 여 Syslog 에이전트를 다시 시작 합니다. `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 이 명령을 실행 하 여 에이전트 로그에 오류가 있는지 확인 합니다. `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>온-프레미스 Linux 서버에서 에이전트 배포

Azure를 사용 하지 않는 경우 전용된 Linux 서버에서 실행 되도록 Azure Sentinel 에이전트를 수동으로 배포 합니다.


1. Sentinel Azure 포털에서 클릭 **데이터 컬렉션** 어플라이언스 유형을 선택 하 고 있습니다.
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
  
## <a name="step-2-validate-connectivity"></a>2단계: 연결 유효성 검사

수준도 로그를 Log Analytics에 나타나기 시작 될 때까지 20 분 정도 걸릴 수 있습니다. 

1. 오른쪽 포트 Syslog 에이전트에 로그를 가져오고 있는지 확인 합니다. 이 명령은 Syslog 에이전트 컴퓨터를 실행 합니다. `tcpdump -A -ni any  port 514 -vv` 이 명령은 Syslog 컴퓨터에 장치에서 스트림 로그를 보여 줍니다. 로그 원본 어플라이언스 오른쪽 시설에 올바른 포트에서에서 수신 되는 있는지 확인 합니다.
2. Syslog 디먼 및 에이전트 간의 통신 인지 확인 합니다. 이 명령은 Syslog 에이전트 컴퓨터를 실행 합니다. `tcpdump -A -ni any  port 25226 -vv` 이 명령은 Syslog 컴퓨터에 장치에서 스트림 로그를 보여 줍니다. 에이전트에서 로그도 수신 되는 있는지 확인 합니다.
3. 성공적인 결과 제공 하는 모두 해당 명령의 경우에 Log Analytics 로그 도착 하는 경우 참조를 확인 합니다. 이러한 어플라이언스에서 스트리밍되는 모든 이벤트에서 Log Analytics에서 원시 형태로 나타나는 `CommonSecurityLog` 형식입니다.
1. 확인 오류가 있는 경우 또는 로그 되지 도착 하는 경우를 확인 하려면 `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. 에 Syslog 메시지 기본 크기는 2048 바이트 (2KB)로 제한 해야 합니다. 로그에 너무 긴 경우에이 명령을 사용 하 여 security_events.conf 업데이트: `message_length_limit 4096`
6. Log Analytics에서 관련 스키마를 사용 하 여 CEF 이벤트를 검색할 **CommonSecurityLog**합니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel CEF 어플라이언스에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

