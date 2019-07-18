---
title: Azure Site Recovery 프로세스 서버 문제 해결
description: 이 문서에서는 Azure Site Recovery 프로세스 서버를 사용 하 여 문제를 해결 하는 방법 설명
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: raynew
ms.openlocfilehash: 6e31308800f72d60381f1e4ecd540482ba263851
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969359"
---
# <a name="troubleshoot-the-process-server"></a>프로세스 서버 문제 해결

합니다 [Site Recovery](site-recovery-overview.md) 온-프레미스 VMware Vm 및 물리적 서버에 대 한 재해 복구 Azure 설정한 경우 프로세스 서버를 사용 합니다. 이 문서에는 복제 및 연결 문제를 포함 하 여 프로세스 서버를 사용 하 여 문제를 해결 하는 방법을 설명 합니다.

[자세한](vmware-physical-azure-config-process-server-overview.md) 프로세스 서버에 대 한 합니다.

## <a name="before-you-start"></a>시작하기 전에

문제 해결 시작 하기 전에:

1. 이해 해야 하는 방법 [프로세스 서버를 모니터링](vmware-physical-azure-monitor-process-server.md)합니다.
2. 아래 모범 사례를 검토 합니다.
3. 따라야 [용량 고려 사항](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 대 한 크기 조정 지침을 사용 하는 [구성 서버](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) 또는 [독립 실행형 프로세스 서버](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)합니다.

## <a name="best-practices-for-process-server-deployment"></a>프로세스 서버 배포에 대 한 모범 사례

프로세스 서버의 최적의 성능을 위해 다양 한 일반적인 모범 사례를 요약해 보았습니다 했습니다.

**모범 사례** | **세부 정보**
--- |---
**사용 현황** | 구성 서버/독립 실행형 프로세스 서버는 원래 용도에 사용 해야 합니다. 컴퓨터에서 다른 작업을 실행 하지 마세요.
**IP 주소** | 프로세스 서버에 고정 IPv4 주소를 구성 하는 NAT 없는 되었는지 확인 합니다.
**메모리/c p U 사용 제어** |70%의 CPU 및 메모리 사용량을 유지 합니다.
**사용 가능한 공간 확인** | 사용 가능한 공간에 프로세스 서버 캐시 디스크 공간을 가리킵니다. 복제 데이터는 Azure에 업로드 되기 전에 캐시에 저장 됩니다.<br/><br/> 25% 이상이 사용 가능한 공간을 유지 합니다. 20% 아래로 떨어지면 복제 프로세스 서버와 연결 된 복제 된 컴퓨터에 대 한 제한 됩니다.

## <a name="check-process-server-health"></a>프로세스 서버 상태 확인

문제를 해결 하는 첫 번째 단계는 프로세스 서버의 상태를 확인 하는 것입니다. 이렇게 하려면 모든 경고를 검토 하는 필요한 서비스가 실행 되 고이 프로세스 서버에서 하트 비트 인지 확인 하는 확인 하세요. 이러한 단계는 다음 그림에 요약 되어 뒤에 단계를 수행 하는 데는 프로시저입니다.

![프로세스 서버 상태 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1단계: 프로세스 서버 상태 경고 문제 해결

프로세스 서버는 다양 한 상태 경고를 생성합니다. 이러한 경고 및 권장 되는 작업은 다음 표에 요약 되어 있습니다.

**경고 유형** | **오류** | **문제 해결**
--- | --- | --- 
![Healthy][green] | 없음  | 프로세스 서버가 연결 되어 있고 정상입니다.
![Warning][yellow] | 지정 된 서비스가 실행 되지 않습니다. | 1. 서비스가 실행 중인지 확인 합니다.<br/> 2. 서비스가 예상 대로 실행 되는 경우 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![Warning][yellow]  | 지난 15 분 동안 CPU 사용률이 > 80%입니다. | 1. 새 컴퓨터를 추가 하지 마세요.<br/>2. 프로세스 서버를 사용 하 여 Vm의 수를 정렬 하는 check [제한을 정의](site-recovery-plan-capacity-vmware.md#capacity-considerations), 설정을 고려 하 고는 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)합니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![중요][red] |  지난 15 분 동안 CPU 사용률이 > 95%입니다. | 1. 새 컴퓨터를 추가 하지 마세요.<br/>2. 프로세스 서버를 사용 하 여 Vm의 수를 정렬 하는 check [제한을 정의](site-recovery-plan-capacity-vmware.md#capacity-considerations), 설정을 고려 하 고는 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)합니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.<br/> 4. 문제가 지속 되 면 실행 합니다 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) VMware/물리적 서버 복제에 대 한 합니다.
![Warning][yellow] | 지난 15 분 동안 > 80%를 사용 하는 메모리입니다. |  1. 새 컴퓨터를 추가 하지 마세요.<br/>2. 프로세스 서버를 사용 하 여 Vm의 수를 정렬 하는 check [제한을 정의](site-recovery-plan-capacity-vmware.md#capacity-considerations), 설정을 고려 하 고는 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)합니다.<br/>3. 경고와 관련 된 지침을 따릅니다.<br/> 4. 문제가 지속 되 면 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![중요][red] | 지난 15 분 동안 > 95%를 사용 하는 메모리입니다. | 1. 새 컴퓨터를 추가 하지 마세요 및 설정을 고려 하는 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)합니다.<br/> 2. 경고와 관련 된 지침을 따릅니다.<br/> 3. 4. 문제가 지속 되는 경우 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.<br/> 4. 문제가 지속 되 면 실행 합니다 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) VMware/물리적 서버 복제 문제에 대 한 합니다.
![Warning][yellow] | 폴더 사용 가능한 공간이 < 30% 지난 15 분 동안 캐시 하 고 있습니다. | 1. 새 컴퓨터를 추가 하 고 설정 고려 하지는 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)합니다.<br/>2. 확인 프로세스 서버를 사용 하 여 Vm의 수를 맞추는 [지침](site-recovery-plan-capacity-vmware.md#capacity-considerations)합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.
![중요][red] |  최근 15 분 동안 사용 가능한 공간이 < 25% | 1. 이 문제에 대 한 경고와 관련 된 지침을 따릅니다.<br/> 2. 3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.<br/> 3. 문제가 지속 되 면 실행 합니다 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner) VMware/물리적 서버 복제에 대 한 합니다.
![중요][red] | 15 분 이상에 대 한 프로세스 서버에서 하트 비트가 없습니다. Tmansvs 서비스는 구성 서버와 통신 하지 못합니다. | 1) 프로세스 서버가 실행 되 고 있는지 확인 합니다.<br/> 2. tmassvc 프로세스 서버에서 실행 되 고 있는지 확인 합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제를 해결](#check-connectivity-and-replication)합니다.


![테이블 키](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2단계: 프로세스 서버 서비스를 확인 합니다.

프로세스 서버에서 실행 되어야 하는 서비스는 다음 표에 요약 되어 있습니다. 프로세스 서버를 배포 하는 방법에 따라 서비스에는 다음과 같은 약간의 차이가 있습니다. 

Microsoft Azure Recovery Services 에이전트 (obengine)를 제외한 모든 서비스를 확인 합니다 StartType로 설정 되어 있는지 **자동** 하거나 **자동 (지연 된 시작)** 합니다.
 
**배포** | **실행 중인 서비스**
--- | ---
**구성 서버에서 프로세스 서버** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; 로그 업로드 서비스 (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services 에이전트 (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; World Wide Web Publishing 서비스 (W3SVC); MySQL; Microsoft Azure Site Recovery 서비스 (dra)
**독립 실행형 서버로 실행 중인 프로세스 서버** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; 로그 업로드 서비스 (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services 에이전트 (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc 합니다.
**장애 복구를 위해 Azure에 배포 하는 프로세스 서버** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; 로그 업로드 서비스 (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>3단계: 프로세스 서버 하트 비트를 확인 합니다.

(오류 코드 806) 프로세스 서버에서 하트 비트의 경우 다음을 수행 합니다.

1. 프로세스 서버 VM을 실행 인지 확인 합니다.
2. 오류에 대 한 이러한 로그를 확인 합니다.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>연결 및 복제를 확인 합니다.

 초기 및 지속적인 복제 실패는 원본 컴퓨터와 프로세스 서버 간에 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 발생 경우가 많습니다. 이러한 단계는 다음 그림에 요약 되어 뒤에 단계를 수행 하는 데는 프로시저입니다.

![연결 및 복제 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4단계: 원본 컴퓨터 시간 동기화 확인

복제 된 컴퓨터가 시스템 날짜/시간 동기화 되는지 확인 합니다. [자세히 알아보기](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5단계: 원본 컴퓨터에서 바이러스 백신 소프트웨어를 확인 합니다.

복제 된 컴퓨터에서 바이러스 백신 소프트웨어 없음 Site Recovery를 차단 하 고 있는지 확인 합니다. 바이러스 백신 프로그램에서 Site Recovery를 제외 해야 할 경우 검토 [이 문서에서는](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)합니다.

## <a name="step-6-check-connectivity-from-source-machine"></a>6단계: 원본 컴퓨터에서 연결 확인


1. 설치 합니다 [텔넷 클라이언트](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) 해야 할 경우 원본 컴퓨터. Ping을 사용 하지 마세요.
2. 원본 컴퓨터에서 텔넷을 사용 하 여 HTTPS 포트에서 프로세스 서버를 ping 합니다. 기본적으로 9443 복제 트래픽에 대 한 HTTPS 포트입니다.

    `telnet <process server IP address> <port>`

3. 연결이 성공적인 지 여부를 확인 합니다.


**연결** | **세부 정보** | **작업**
--- | --- | ---
**Successful** | 텔넷 빈 화면을 표시 및 프로세스 서버에 연결할 수 있습니다. | 더 이상 동작이 필요합니다.
**실패 한** | 연결할 수 없습니다. | 프로세스 서버에서 인바운드 포트 9443 허용 되어 있는지 확인 합니다. 예를 들어, 경계 네트워크 또는 스크린된 된 서브넷이 있는 경우. 연결을 다시 확인 합니다.
**부분적으로 성공** | 에 연결할 수는 있지만 원본 컴퓨터 보고 프로세스 서버를 연결할 수 없습니다. | 다음 문제 해결 절차를 사용 하 여 계속 합니다.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7단계: 접근할 수 없는 프로세스 서버를 문제 해결

프로세스 서버가 원본 컴퓨터에서 연결할 수 없으면 78186 오류가 표시 됩니다. 해결 하지 않을 경우이 문제는 모두 앱 일치 시킬 및 크래시 일관성 복구 지점 예상 대로 생성 되지 않습니다.

원본 컴퓨터 프로세스 서버의 IP 주소에 도달 하 고 종단 간 연결을 확인 하려면 원본 컴퓨터 cxpsclient 도구를 실행할 수 있는지 여부를 확인 하 여이 문제를 해결 합니다.


### <a name="check-the-ip-connection-on-the-process-server"></a>프로세스 서버에서 IP 연결 확인

텔넷 되었습니다. 원본 컴퓨터가 프로세스 서버를 연결할 수 없습니다를 보고 하지만 경우에 프로세스 서버의 IP 주소를 연결할 수 있는지 여부를 확인 합니다.

1. 웹 브라우저에서 IP 주소 https://<PS_IP>:<PS_Data_Port>/ 도달 하려고 합니다.
2. 이 검사는 HTTPS 인증서 오류가 표시 되 면 정상입니다. 오류를 무시 하면 400-잘못 된 요청 표시 됩니다. 즉, 서버 브라우저 요청을 처리할 수 없습니다는 표준 HTTPS 연결 장치가 정상입니다.
3. 이 검사도 소용이 없으면 브라우저 오류 메시지를 다음 note 합니다. 예를 들어, 407 오류 프록시 인증 문제가 표시 됩니다.

### <a name="check-the-connection-with-cxpsclient"></a>Cxpsclient 사용 하 여 연결 확인

또한 종단 간 연결을 확인 하려면 cxpsclient 도구를 실행할 수 있습니다.

1. 다음과 같이 도구를 실행 합니다.

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 프로세스 서버에서 이러한 폴더에 생성된 된 로그를 확인 합니다.

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>업로드 실패 (오류 78028)에 대 한 원본 VM 로그를 확인 합니다.

생성 되지 않는 두 크래시 일관성 및 앱 일치 복구 지점에 데이터 업로드 프로세스에 원본 컴퓨터에서 차단 문제가 발생할 수 있습니다. 

1. 네트워크 업로드 오류를 해결 하려면이 로그에서 오류를 찾을 수 있습니다.

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. 이 문서의 절차의 나머지를 사용 하 여 데이터 업로드 문제 해결에 도움이 됩니다.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8단계: 프로세스 서버에서 데이터를 푸시하는 지 여부를 확인 합니다.

Azure에 프로세스 서버는 데이터를 푸시하 적극적으로 하는지 여부를 확인 합니다.

  1. 프로세스 서버에서 작업 관리자를 엽니다(Ctrl+Shift+Esc 누르기).
  2. 선택 된 **성능** 탭 > **리소스 모니터 열기**합니다.
  3. **리소스 모니터** 페이지에서 선택 합니다 **네트워크** 탭 합니다. 아래 **네트워크 활동을 사용 하 여 프로세스**, cbengine.exe가 적극적으로 데이터의 큰 vNotolume를 보내고 있는지 여부를 확인 합니다.

       ![네트워크 활동을 사용 하 여 프로세스에서 볼륨](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  cbengine.exe가 대용량 데이터를 전송하고 있지 않으면 다음 섹션의 단계를 완료합니다.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9단계: Azure blob storage에 프로세스 서버 연결을 확인

1. 리소스 모니터에서 선택 **cbengine.exe**합니다.
2. 아래 **TCP 연결**, 프로세스 서버에서 연결 된 Azure storage에 있는지 여부를 확인 합니다.

  ![Cbengine.exe와 Azure Blob 저장소 URL로 연결](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>서비스 확인

프로세스 서버에서 Azure blob 저장소 URL에 연결 되지 않은 경우 서비스가 실행 중인지 확인 합니다.

1. 제어판에서 선택 **Services**합니다.
2. 다음 서비스가 실행 되 고 있는지 확인 합니다.

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services 에이전트
    - Microsoft Azure Site Recovery 서비스
    - tmansvc

3. 실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다.
4. 프로세스 서버를 연결 하 고 연결할 수 있는지 확인 합니다. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10 단계: Azure 공용 IP 주소에 프로세스 서버 연결을 확인 하십시오.

1. 프로세스 서버에서의 **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**에서 최신 CBEngineCurr.errlog 파일을 엽니다.
2. 파일에서 검색할 **443**, 또는 문자열 **연결 시도가 실패 했습니다**합니다.

  ![Temp 폴더에 오류 로그](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 문제를 표시 하는 경우 포트 443을 사용 하 여 Azure 공용 IP 주소를 CBEngineCurr.currLog 파일에 있습니다.

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 프로세스 서버 명령줄에서 텔넷을 사용 하 여 Azure 공용 IP 주소를 ping 합니다.
6. 연결할 수 없으면 다음 절차를 따릅니다.

## <a name="step-11-check-process-server-firewall-settings"></a>11단계: 프로세스 서버 방화벽 설정을 확인 합니다. 

프로세스 서버의 IP 주소 기반 방화벽이 액세스를 차단 하 고 있는지 여부를 확인 합니다.

1. IP 주소 기반 방화벽 규칙:

    a)의 전체 목록을 다운로드 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)합니다.

    b) 방화벽이 Azure로 (및 기본 HTTPS 포트 443) 통신을 허용 하도록 방화벽 구성에 IP 주소 범위를 추가 합니다.

    c) 허용 IP 주소 범위 (액세스 제어 및 id 관리에 사용) 미국 서 부 Azure 지역 및 구독의 Azure 지역에 대 한 합니다.

2. URL 기반 방화벽에 대 한 방화벽을 구성 하려면 다음 표에 나열 된 Url을 추가 합니다.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12단계: 프로세스 서버의 프록시 설정을 확인합니다 

1. 프록시 서버를 사용하는 경우 DNS 서버에서 프록시 서버 이름을 확인하는지 확인합니다. 레지스트리 키에서 구성 서버를 설정할 때 제공한 값을 확인 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**합니다.
2. 데이터를 보내도록 Azure Site Recovery 에이전트에서 동일한 설정을 사용 하는지 확인 합니다.

    a) 검색 **Microsoft Azure Backup**합니다.

    b) 오픈 **Microsoft Azure Backup**, 선택한 **동작** > **속성 변경**합니다.

    c)는 **프록시 구성을** 탭 프록시 주소는 레지스트리 설정에 표시 되는 프록시 주소와 동일한 이어야 합니다. 그렇지 않은 경우 동일한 주소로 변경하세요.

## <a name="step-13-check-bandwidth"></a>13단계: 대역폭 확인

프로세스 서버와 Azure 간의 대역폭 늘리고 문제가 여전히 발생 하는지 여부를 확인 합니다.


## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png