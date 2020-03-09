---
title: Azure Site Recovery 프로세스 서버 문제 해결
description: 이 문서에서는 Azure Site Recovery 프로세스 서버와 관련 된 문제를 해결 하는 방법을 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395025"
---
# <a name="troubleshoot-the-process-server"></a>프로세스 서버 문제 해결

[Site Recovery](site-recovery-overview.md) 프로세스 서버는 온-프레미스 VMware vm 및 물리적 서버에 대해 Azure에 대 한 재해 복구를 설정할 때 사용 됩니다. 이 문서에서는 복제 및 연결 문제를 포함 하 여 프로세스 서버와 관련 된 문제를 해결 하는 방법을 설명 합니다.

프로세스 서버에 대해 [자세히 알아보세요](vmware-physical-azure-config-process-server-overview.md) .

## <a name="before-you-start"></a>시작하기 전 확인 사항

문제 해결을 시작 하기 전에:

1. [프로세스 서버를 모니터링](vmware-physical-azure-monitor-process-server.md)하는 방법을 이해 해야 합니다.
2. 아래 모범 사례를 검토 하세요.
3. [용량 고려 사항을](site-recovery-plan-capacity-vmware.md#capacity-considerations)따르고 [구성 서버](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) 또는 [독립 실행형 프로세스 서버](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)에 대 한 크기 조정 지침을 사용 해야 합니다.

## <a name="best-practices-for-process-server-deployment"></a>프로세스 서버 배포에 대 한 모범 사례

프로세스 서버의 성능을 최적화 하기 위해 몇 가지 일반적인 모범 사례를 요약 했습니다.

**모범 사례** | **세부 정보**
--- |---
**사용 현황** | 구성 서버/독립 실행형 프로세스 서버가 목적 으로만 사용 되는지 확인 합니다. 컴퓨터에서 다른 항목을 실행 하지 마세요.
**IP 주소** | 프로세스 서버에 고정 IPv4 주소가 있는지 확인 하 고 NAT를 구성 하지 않았는지 확인 합니다.
**메모리/CPU 사용량 제어** |CPU 및 메모리 사용량을 70% 미만으로 유지 합니다.
**여유 공간 확인** | 사용 가능한 공간은 프로세스 서버의 캐시 디스크 공간을 나타냅니다. 복제 데이터는 Azure에 업로드 되기 전에 캐시에 저장 됩니다.<br/><br/> 사용 가능한 공간을 25% 이상으로 유지 합니다. 20% 미만이 면 프로세스 서버와 연결 된 복제 된 컴퓨터에 대해 복제가 제한 됩니다.

## <a name="check-process-server-health"></a>프로세스 서버 상태 확인

문제 해결의 첫 번째 단계는 프로세스 서버의 상태를 확인 하는 것입니다. 이렇게 하려면 모든 경고를 검토 하 고 필요한 서비스가 실행 중인지 확인 한 다음 프로세스 서버에서 하트 비트가 있는지 확인 합니다. 이러한 단계는 다음 그림에 요약 되어 있으며 단계를 수행 하는 데 도움이 되는 절차를 따릅니다.

![프로세스 서버 상태 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1 단계: 프로세스 서버 상태 경고 문제 해결

프로세스 서버에서 다양 한 상태 경고를 생성 합니다. 다음 표에서는 이러한 경고 및 권장 조치를 요약 하 여 설명 합니다.

**경고 유형** | **오류** | **문제 해결**
--- | --- | --- 
![정상][green] | 없음  | 프로세스 서버가 연결 되어 있고 정상 상태입니다.
![경고][yellow] | 지정한 서비스가 실행 되 고 있지 않습니다. | 1. 서비스가 실행 중인지 확인 합니다.<br/> 2. 서비스가 예상 대로 실행 되는 경우 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![경고][yellow]  | 최근 15 분 동안 CPU 사용률이 80% >. | 1. 새 컴퓨터를 추가 하지 않습니다.<br/>2. 프로세스 서버를 사용 하는 Vm 수가 [정의 된 제한](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합 하는지 확인 하 고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정 하는 것이 좋습니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![중요][red] |  최근 15 분 동안 CPU 사용률이 95% >. | 1. 새 컴퓨터를 추가 하지 않습니다.<br/>2. 프로세스 서버를 사용 하는 Vm 수가 [정의 된 제한](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합 하는지 확인 하 고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정 하는 것이 좋습니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.<br/> 4. 문제가 계속 되 면 VMware/물리적 서버 복제에 대 한 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) 를 실행 합니다.
![경고][yellow] | 최근 15 분 동안 메모리 사용 > 80%입니다. |  1. 새 컴퓨터를 추가 하지 않습니다.<br/>2. 프로세스 서버를 사용 하는 Vm 수가 [정의 된 제한](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합 하는지 확인 하 고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정 하는 것이 좋습니다.<br/>3. 경고와 관련 된 지침을 따르세요.<br/> 4. 문제가 계속 되 면 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![중요][red] | 최근 15 분 동안 메모리 사용 > 95%입니다. | 1. 새 컴퓨터를 추가 하거나 추가 [프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정 하는 것을 고려 하지 않습니다.<br/> 2. 경고와 관련 된 지침을 따르세요.<br/> 3. 4. 문제가 계속 되 면 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)하십시오.<br/> 4. 문제가 계속 되 면 VMware/물리적 서버 복제 문제에 대 한 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) 를 실행 합니다.
![경고][yellow] | 마지막 15 분 동안 캐시 폴더의 사용 가능한 공간 < 30%입니다. | 1. 새 컴퓨터를 추가 하지 말고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정 하는 것이 좋습니다.<br/>2. 프로세스 서버를 사용 하는 Vm 수가 [지침](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합 하는지 확인 합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![중요][red] |  최근 15 분 동안 사용 가능한 공간 < 25% | 1 .이 문제에 대 한 경고와 관련 된 지침을 따릅니다.<br/> 2. 3. [연결 및 복제 문제를 해결](#check-connectivity-and-replication)하려면 아래 지침을 따르세요.<br/> 3. 문제가 계속 되 면 VMware/물리적 서버 복제에 대 한 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) 를 실행 합니다.
![중요][red] | 15 분 이상 동안 프로세스 서버에서 하트 비트가 없습니다. Tmansvs 서비스가 구성 서버와 통신 하지 않습니다. | 1) 프로세스 서버가 실행 중인지 확인 합니다.<br/> 2. 프로세스 서버에서 tmassvc가 실행 되 고 있는지 확인 합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.


![테이블 키](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2 단계: 프로세스 서버 서비스 확인

프로세스 서버에서 실행 해야 하는 서비스는 다음 표에 요약 되어 있습니다. 프로세스 서버 배포 방법에 따라 서비스에 약간의 차이가 있습니다. 

Microsoft Azure Recovery Services 에이전트 (obengine)를 제외한 모든 서비스에 대해 StartType이 **자동** 또는 **자동 (지연 된 시작)** 으로 설정 되어 있는지 확인 합니다.
 
**배포** | **실행 중인 서비스**
--- | ---
**구성 서버의 프로세스 서버** | Scaleout-processserver ProcessServerMonitor; cxprocessserver InMage PushInstall; 로그 업로드 서비스 (LogUpload); InMage Scout 응용 프로그램 서비스; Microsoft Azure Recovery Services 에이전트 (obengine); InMage Scout VX Agent-센티널/감시 (svagents); tmansvc W3SVC (World Wide Web Publishing Service); MySQL Dra (Microsoft Azure Site Recovery 서비스)
**독립 실행형 서버로 실행 되는 프로세스 서버** | Scaleout-processserver ProcessServerMonitor; cxprocessserver InMage PushInstall; 로그 업로드 서비스 (LogUpload); InMage Scout 응용 프로그램 서비스; Microsoft Azure Recovery Services 에이전트 (obengine); InMage Scout VX Agent-센티널/감시 (svagents); tmansvc.
**장애 복구를 위해 Azure에 배포 된 프로세스 서버** | Scaleout-processserver ProcessServerMonitor; cxprocessserver InMage PushInstall; 로그 업로드 서비스 (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>3 단계: 프로세스 서버 하트 비트 확인

프로세스 서버에서 하트 비트가 없으면 (오류 코드 806) 다음을 수행 합니다.

1. 프로세스 서버 VM이 실행 중인지 확인 합니다.
2. 이러한 로그에서 오류를 확인 합니다.

    C:\ProgramData\ASR\home\svsystems\eventmanager*c\programdata\asr\home\svsystems\ monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>연결 및 복제 확인

 초기 및 진행 중인 복제 오류는 원본 컴퓨터와 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 발생 하는 경우가 많습니다. 이러한 단계는 다음 그림에 요약 되어 있으며 단계를 수행 하는 데 도움이 되는 절차를 따릅니다.

![연결 및 복제 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4 단계: 원본 컴퓨터에서 시간 동기화 확인

복제 된 컴퓨터의 시스템 날짜/시간이 동기화 되어 있는지 확인 하십시오. [자세히 알아보기](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5 단계: 원본 컴퓨터의 바이러스 백신 소프트웨어 확인

복제 된 컴퓨터에 바이러스 백신 소프트웨어가 Site Recovery 차단 되 고 있지 않은지 확인 합니다. 바이러스 백신 프로그램에서 Site Recovery를 제외 해야 하는 경우 [이 문서](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)를 검토 하세요.

## <a name="step-6-check-connectivity-from-source-machine"></a>6 단계: 원본 컴퓨터에서 연결 확인


1. 필요한 경우 원본 컴퓨터에 [텔넷 클라이언트](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) 를 설치 합니다. Ping을 사용 하지 마세요.
2. 원본 컴퓨터에서 텔넷을 사용 하 여 HTTPS 포트의 프로세스 서버에 대해 ping을 수행 합니다. 기본적으로 9443는 복제 트래픽에 대 한 HTTPS 포트입니다.

    `telnet <process server IP address> <port>`

3. 연결에 성공 했는지 여부를 확인 합니다.


**연결** | **세부 정보** | **동작**
--- | --- | ---
**올바르게** | 텔넷은 빈 화면을 표시 하 고 프로세스 서버에 연결할 수 있습니다. | 추가 작업이 필요 하지 않습니다.
**못한** | 연결할 수 없음 | 프로세스 서버에서 인바운드 포트 9443이 허용 되는지 확인 합니다. 예를 들어 경계 네트워크 또는 스크린 된 서브넷이 있는 경우입니다. 연결을 다시 확인 합니다.
**부분적으로 성공** | 연결할 수 있지만 원본 컴퓨터에서 프로세스 서버에 연결할 수 없다고 보고 합니다. | 다음 문제 해결 절차를 계속 진행 합니다.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7 단계: 연결할 수 없는 프로세스 서버 문제 해결

원본 컴퓨터에서 프로세스 서버에 연결할 수 없는 경우 오류 78186이 표시 됩니다. 주소가 지정 되지 않은 경우이 문제로 인해 앱 일치 및 크래시 일치 복구 지점이 예상 대로 생성 되지 않습니다.

원본 컴퓨터가 프로세스 서버의 IP 주소에 연결할 수 있는지 확인 하 고 원본 컴퓨터에서 cxpsclient 도구를 실행 하 여 종단 간 연결을 확인 하 여 문제를 해결 합니다.


### <a name="check-the-ip-connection-on-the-process-server"></a>프로세스 서버에서 IP 연결 확인

텔넷에 성공 했지만 원본 컴퓨터에서 프로세스 서버에 연결할 수 없다고 보고 하는 경우 프로세스 서버의 IP 주소에 연결할 수 있는지 확인 합니다.

1. 웹 브라우저에서 IP 주소 https://< PS_IP >: < PS_Data_Port >/에 연결 하려고 합니다.
2. 이 검사에는 HTTPS 인증서 오류가 표시 되 면 정상입니다. 오류를 무시 하면 400-잘못 된 요청이 표시 됩니다. 즉, 서버에서 브라우저 요청을 처리할 수 없으며 표준 HTTPS 연결에 문제가 없는 것입니다.
3. 이 확인이 작동 하지 않으면 브라우저 오류 메시지를 확인 합니다. 예를 들어 407 오류는 프록시 인증에 문제가 있음을 의미 합니다.

### <a name="check-the-connection-with-cxpsclient"></a>Cxpsclient를 사용 하 여 연결 확인

또한 cxpsclient 도구를 실행 하 여 종단 간 연결을 확인할 수 있습니다.

1. 다음과 같이 도구를 실행합니다.

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 프로세스 서버에서 다음 폴더에 생성 된 로그를 확인 합니다.

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>원본 VM 로그에서 업로드 실패 확인 (오류 78028)

원본 컴퓨터에서 프로세스 서비스로 차단 된 데이터 업로드 문제로 인해 크래시 일관성 및 앱 일치 복구 지점이 생성 되지 않을 수 있습니다. 

1. 네트워크 업로드 실패 문제를 해결 하려면이 로그에서 오류를 확인할 수 있습니다.

    C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\svagents * .log 

2. 이 문서의 나머지 절차를 사용 하 여 데이터 업로드 문제를 해결할 수 있습니다.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8 단계: 프로세스 서버에서 데이터를 푸시하는 지 확인

프로세스 서버에서 Azure로 데이터를 적극적으로 푸시하는 지 확인 합니다.

  1. 프로세스 서버에서 작업 관리자를 엽니다(Ctrl+Shift+Esc 누르기).
  2. **성능** 탭을 선택 하 > **리소스 모니터를 엽니다**.
  3. **리소스 모니터** 페이지에서 **네트워크** 탭을 선택 합니다. **네트워크 활동을 사용 하는 프로세스**에서, cbengine .exe가 대량의 데이터를 적극적으로 전송 하 고 있는지 확인 합니다.

       ![네트워크 활동을 사용 하는 프로세스 아래의 볼륨](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  cbengine.exe가 대용량 데이터를 전송하고 있지 않으면 다음 섹션의 단계를 완료합니다.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9 단계: Azure blob 저장소에 대 한 프로세스 서버 연결 확인

1. 리소스 모니터에서 **cbengine .exe**를 선택 합니다.
2. **TCP 연결**에서 프로세스 서버에서 Azure storage로의 연결이 있는지 확인 합니다.

  ![Cbengine Azure Blob storage URL 간의 연결](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>서비스 확인

프로세스 서버에서 Azure blob storage URL로 연결 되지 않은 경우 서비스가 실행 중인지 확인 합니다.

1. 제어판에서 **서비스**를 선택 합니다.
2. 다음 서비스가 실행 중인지 확인 합니다.

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services 에이전트
    - Microsoft Azure Site Recovery 서비스
    - tmansvc

3. 실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다.
4. 프로세스 서버가 연결 되어 있고 연결할 수 있는지 확인 하십시오. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10 단계: Azure 공용 IP 주소에 대 한 프로세스 서버 연결 확인

1. 프로세스 서버의 **%Programfiles%\Microsoft Azure Recovery Services Agent\Temp**에서 최신 cbenginecurr.errlog 로그 파일을 엽니다.
2. 파일에서 **443**을 검색 하거나 문자열 **연결 시도가 실패**한 경우를 검색 합니다.

  ![임시 폴더의 오류 로그](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 문제가 표시 되 면 포트 443을 사용 하 여 Cbenginecurr.errlog 로그 파일에 Azure 공용 IP 주소를 배치 합니다.

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 프로세스 서버의 명령줄에서 Telnet을 사용 하 여 Azure 공용 IP 주소를 ping 합니다.
6. 연결할 수 없는 경우 다음 절차를 수행 합니다.

## <a name="step-11-check-process-server-firewall-settings"></a>11 단계: 프로세스 서버 방화벽 설정을 확인 합니다. 

프로세스 서버의 IP 주소 기반 방화벽이 액세스를 차단 하는지 여부를 확인 합니다.

1. IP 주소 기반 방화벽 규칙의 경우:

    a) [Microsoft Azure 데이터 센터 IP 범위의](https://www.microsoft.com/download/details.aspx?id=41653)전체 목록을 다운로드 합니다.

    b) 방화벽 구성에 IP 주소 범위를 추가 하 여 방화벽이 Azure와의 통신을 허용 하는지 확인 합니다 (기본 HTTPS 포트 443).

    c) 구독의 Azure 지역 및 Azure 미국 서 부 지역의 IP 주소 범위를 허용 합니다 (액세스 제어 및 id 관리에 사용 됨).

2. URL 기반 방화벽의 경우 다음 표에 나열 된 Url을 방화벽 구성에 추가 합니다.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12 단계: 프로세스 서버 프록시 설정 확인 

1. 프록시 서버를 사용하는 경우 DNS 서버에서 프록시 서버 이름을 확인하는지 확인합니다. 레지스트리 키 **HKEY_LOCAL_MACHINE \Software\microsoft\azure Site Recovery\ProxySettings**에서 구성 서버를 설정할 때 입력 한 값을 확인 합니다.
2. Azure Site Recovery 에이전트가 데이터를 보내기 위해 동일한 설정을 사용 해야 합니다.

    a) **Microsoft Azure Backup**를 검색 합니다.

    b) **Microsoft Azure Backup**을 열고 **동작** 을 선택 하 > **속성을 변경**합니다.

    c) **프록시 구성** 탭에서 프록시 주소는 레지스트리 설정에 표시 되는 프록시 주소와 동일 해야 합니다. 그렇지 않은 경우 동일한 주소로 변경하세요.

## <a name="step-13-check-bandwidth"></a>13 단계: 대역폭 확인

프로세스 서버와 Azure 간의 대역폭을 늘린 다음 문제가 계속 발생 하는지 확인 합니다.


## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
