---
title: Azure Site Recovery 프로세스 서버 문제 해결
description: 이 문서에서는 Azure Site Recovery 프로세스 서버와 관련된 문제를 해결하는 방법을 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 7657d614645bb00235db2701773bc15fa260b70d
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835804"
---
# <a name="troubleshoot-the-process-server"></a>프로세스 서버 문제 해결

[Site Recovery](site-recovery-overview.md) 프로세스 서버는 온-프레미스 VMware VM 및 물리적 서버를 대상으로 Azure에 재해 복구를 설정할 때 사용됩니다. 이 문서에서는 복제 및 연결 문제를 포함하여 프로세스 서버와 관련된 문제를 해결하는 방법을 설명합니다.

프로세스 서버에 대해 [자세히 알아보세요](vmware-physical-azure-config-process-server-overview.md).

## <a name="before-you-start"></a>시작하기 전에

문제 해결을 시작하기 전 확인 사항

1. [프로세스 서버를 모니터링](vmware-physical-azure-monitor-process-server.md)하는 방법을 이해해야 합니다.
2. 아래 모범 사례를 검토합니다.
3. [용량 고려 사항](site-recovery-plan-capacity-vmware.md#capacity-considerations)을 따르고 [구성 서버](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) 또는 [독립 실행형 프로세스 서버](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)에 대한 크기 조정 지침을 사용합니다.

## <a name="best-practices-for-process-server-deployment"></a>프로세스 서버 배포에 대한 모범 사례

프로세스 서버의 성능을 최적화하기 위해 몇 가지 일반적인 모범 사례를 요약했습니다.

**모범 사례**: | **세부 정보**
--- |---
**사용 현황** | 구성 서버/독립 실행형 프로세스 서버가 의도된 목적으로만 사용되는지 확인합니다. 머신에서 다른 항목을 실행하면 안 됩니다.
**IP 주소** | 프로세스 서버에 고정 IPv4 주소가 있는지 확인하고 NAT를 구성하지 않았는지 확인합니다.
**메모리/CPU 사용량 제어** |CPU 및 메모리 사용량을 70% 미만으로 유지합니다.
**사용 가능한 공간 확인** | 사용 가능한 공간은 프로세스 서버의 캐시 디스크 공간을 나타냅니다. 복제 데이터는 Azure에 업로드되기 전에 캐시에 저장됩니다.<br/><br/> 사용 가능한 공간을 25% 이상으로 유지합니다. 20% 미만이면 프로세스 서버와 연결된 복제된 머신에 대해 복제가 제한됩니다.

## <a name="check-process-server-health"></a>프로세스 서버 상태 확인

문제 해결의 첫 번째 단계는 프로세스 서버의 상태를 확인하는 것입니다. 이렇게 하려면 모든 경고를 검토하고 필요한 서비스가 실행 중인지 확인한 다음, 프로세스 서버에서 하트비트가 있는지 확인합니다. 이러한 단계는 다음 그림에 요약되어 있으며 뒤이어 단계를 수행하는 데 도움이 되는 절차가 나옵니다.

![프로세스 서버 상태 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1단계: 프로세스 서버 상태 경고 문제 해결

프로세스 서버에서 다양한 상태 경고를 생성합니다. 다음 표에서는 이러한 경고 및 권장 조치를 요약하여 설명합니다.

**경고 유형** | **오류** | **문제 해결**
--- | --- | --- 
![Healthy][green] | None  | 프로세스 서버가 연결되어 있고 정상 상태입니다.
![Warning][yellow] | 지정한 서비스가 실행되고 있지 않습니다. | 1. 서비스가 실행 중인지 확인합니다.<br/> 2. 서비스가 예상대로 실행되는 경우 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.
![Warning][yellow]  | 지난 15분 동안 CPU 사용률이 80% 초과입니다. | 1. 새 머신을 추가하면 안 됩니다.<br/>2. 프로세스 서버를 사용하는 VM 수가 [정의된 제한](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합하는지 확인하고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정하는 것이 좋습니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.
![위험][red] |  지난 15분 동안 CPU 사용률이 95% 초과입니다. | 1. 새 머신을 추가하면 안 됩니다.<br/>2. 프로세스 서버를 사용하는 VM 수가 [정의된 제한](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합하는지 확인하고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정하는 것이 좋습니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.<br/> 4. 문제가 지속되면 VMware/물리적 서버 복제에 대해 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner)를 실행합니다.
![Warning][yellow] | 지난 15분 동안 메모리 사용량이 80% 초과입니다. |  1. 새 머신을 추가하면 안 됩니다.<br/>2. 프로세스 서버를 사용하는 VM 수가 [정의된 제한](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합하는지 확인하고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정하는 것이 좋습니다.<br/>3. 경고와 관련된 지침을 따르세요.<br/> 4. 문제가 지속되면 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.
![위험][red] | 지난 15분 동안 메모리 사용량이 95% 초과입니다. | 1. 새 머신을 추가하지 말고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md) 설정을 고려합니다.<br/> 2. 경고와 관련된 지침을 따르세요.<br/> 3. 4. 문제가 지속되면 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.<br/> 4. 문제가 지속되면 VMware/물리적 서버 복제 문제에 대해 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner)를 실행합니다.
![Warning][yellow] | 지난 15분 동안 캐시 폴더의 사용 가능한 공간이 30% 미만입니다. | 1. 새 머신을 추가하지 말고 [추가 프로세스 서버](vmware-azure-set-up-process-server-scale.md) 설정을 고려합니다.<br/>2. 프로세스 서버를 사용하는 VM 수가 [지침](site-recovery-plan-capacity-vmware.md#capacity-considerations)에 부합하는지 확인합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.
![위험][red] |  지난 15분 동안 사용 가능한 공간이 25% 미만입니다. | 1. 이 문제에 대한 경고와 관련된 지침을 따릅니다.<br/> 2. 3. 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.<br/> 3. 문제가 지속되면 VMware/물리적 서버 복제에 대해 [Deployment Planner](https://aka.ms/asr-v2a-deployment-planner)를 실행합니다.
![위험][red] | 15분 이상 동안 프로세스 서버에서 하트비트가 없습니다. tmansvs 서비스가 구성 서버와 통신하지 않습니다. | 1) 프로세스 서버가 실행 중인지 확인합니다.<br/> 2. 프로세스 서버에서 tmassvc가 실행되고 있는지 확인합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제 해결](#check-connectivity-and-replication)을 수행합니다.


![테이블 키](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2단계: 프로세스 서버 서비스 확인

프로세스 서버에서 실행해야 하는 서비스는 다음 표에 요약되어 있습니다. 프로세스 서버 배포 방법에 따라 서비스에 약간의 차이가 있습니다. 

Microsoft Azure Recovery Services 에이전트(obengine)를 제외한 모든 서비스에 대해 StartType이 **자동** 또는 **자동(지연된 시작)** 으로 설정되어 있는지 확인합니다.
 
**배포** | **실행 중인 서비스**
--- | ---
**구성 서버의 프로세스 서버** | ProcessServer, ProcessServerMonitor, cxprocessserver, InMage PushInstall, Log Upload Service(LogUpload), InMage Scout Application Service, Microsoft Azure Recovery Services Agent(obengine), InMage Scout VX Agent-Sentinel/Outpost(svagents), tmansvc, World Wide Web Publishing 서비스(W3SVC), MySQL, Microsoft Azure Site Recovery Service(dra)
**독립 실행형 서버로 실행되는 프로세스 서버** | ProcessServer, ProcessServerMonitor, cxprocessserver, InMage PushInstall, Log Upload Service(LogUpload), InMage Scout Application Service, Microsoft Azure Recovery Services Agent(obengine), InMage Scout VX Agent-Sentinel/Outpost(svagents), tmansvc
**장애 복구(failback)를 위해 Azure에 배포된 프로세스 서버** | ProcessServer, ProcessServerMonitor, cxprocessserver, InMage PushInstall, Log Upload Service(LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>3단계: 프로세스 서버 하트비트 확인

프로세스 서버에서 하트비트가 없으면(오류 코드 806) 다음을 수행합니다.

1. 프로세스 서버 VM이 실행 중인지 확인합니다.
2. 이러한 로그에서 오류를 확인합니다.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>연결 및 복제 확인

 종종 원본 머신과 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 초기 복제 및 진행 중 복제가 실패할 수 있습니다. 이러한 단계는 다음 그림에 요약되어 있으며 뒤이어 단계를 수행하는 데 도움이 되는 절차가 나옵니다.

![연결 및 복제 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4단계: 원본 머신에서 시간 동기화 확인

복제된 머신의 시스템 날짜/시간이 동기화되어 있는지 확인합니다. [자세히 알아보기](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5단계: 원본 머신의 바이러스 백신 소프트웨어를 확인합니다.

복제된 머신에 바이러스 백신 소프트웨어가 Site Recovery를 차단하고 있지 않은지 확인합니다. 바이러스 백신 프로그램에서 Site Recovery를 제외해야 하는 경우 [관련 문서](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)를 검토합니다.

## <a name="step-6-check-connectivity-from-source-machine"></a>6단계: 원본 머신의 연결 확인


1. 필요한 경우 원본 머신에 [텔넷 클라이언트](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)를 설치합니다. Ping을 사용하면 안 됩니다.
2. 원본 머신에서 텔넷을 사용하여 HTTPS 포트의 프로세스 서버에 대해 ping을 수행합니다. 기본적으로 9443은 복제 트래픽에 대한 HTTPS 포트입니다.

    `telnet <process server IP address> <port>`

3. 연결이 성공했는지를 확인합니다.


**연결** | **세부 정보** | **동작**
--- | --- | ---
**성공** | 텔넷은 빈 화면을 표시하고 프로세스 서버에 연결할 수 있습니다. | 추가 작업이 필요하지 않습니다.
**실패** | 연결할 수 없습니다. | 프로세스 서버에서 인바운드 포트 9443이 허용되는지 확인합니다. 예를 들어 경계 네트워크 또는 스크린된 서브넷이 있는 경우입니다. 연결을 다시 확인합니다.
**부분적으로 성공** | 연결할 수 있지만 원본 머신에서 프로세스 서버에 접근할 수 없다고 보고합니다. | 다음 문제 해결 절차를 계속 진행합니다.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7단계: 접근 불가 프로세스 서버 문제 해결

원본 머신에서 프로세스 서버에 접근할 수 없는 경우 오류 78186이 표시됩니다. 해결되지 않으면 이 문제로 인해 앱 일관성이 있는 복구 지점과 크래시 일관성이 있는 복구 지점이 예상대로 생성되지 않습니다.

원본 머신이 프로세스 서버의 IP 주소에 접근할 수 있는지 확인하고 원본 머신에서 cxpsclient 도구를 실행하여 엔드투엔드 연결을 확인함으로써 문제를 해결합니다.


### <a name="check-the-ip-connection-on-the-process-server"></a>프로세스 서버에서 IP 연결 확인

텔넷은 성공적이지만 원본 머신에서 프로세스 서버에 접근할 수 없다고 보고하는 경우 프로세스 서버의 IP 주소에 연결할 수 있는지 확인합니다.

1. 웹 브라우저에서 IP 주소 https://<PS_IP>:<PS_Data_Port>/에 연결을 시도합니다.
2. 이 검사에서 HTTPS 인증서 오류가 표시되면 정상입니다. 오류를 무시하면 400 - 잘못된 요청이 표시됩니다. 즉, 서버에서 브라우저 요청을 처리할 수 없으며 표준 HTTPS 연결에 문제가 없는 것입니다.
3. 이 검사로 충분하지 않으면 브라우저 오류 메시지를 확인합니다. 예를 들어 407 오류는 프록시 인증에 문제가 있음을 의미합니다.

### <a name="check-the-connection-with-cxpsclient"></a>cxpsclient를 사용하여 연결 확인

또한 cxpsclient 도구를 실행하여 엔드투엔드 연결을 확인할 수 있습니다.

1. 다음과 같이 도구를 실행합니다.

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 프로세스 서버에서 다음 폴더에 생성된 로그를 확인합니다.

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>원본 VM 로그에서 업로드 실패 확인(오류 78028)

원본 머신에서 프로세스 서비스로 데이터 업로드가 차단된 문제로 인해 크래시 일관성이 있는 복구 지점과 앱 일관성이 있는 복구 지점이 생성되지 않을 수 있습니다. 

1. 네트워크 업로드 실패 문제를 해결하려면 다음 로그에서 오류를 확인할 수 있습니다.

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. 이 문서의 나머지 절차에 따라 데이터 업로드 문제를 해결할 수 있습니다.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8단계: 프로세스 서버가 데이터를 Azure로 푸시하는지 확인

프로세스 서버가 데이터를 Azure로 적극적으로 푸시하는지 확인합니다.

  1. 프로세스 서버에서 작업 관리자를 엽니다(Ctrl+Shift+Esc 누르기).
  2. **성능** 탭 > **리소스 모니터 열기**를 선택합니다.
  3. **리소스 모니터** 페이지에서 **네트워크** 탭을 선택합니다. **네트워크 활동이 있는 프로세스**에서 cbengine.exe가 현재 대용량 데이터를 전송하고 있는지 확인합니다.

       ![네트워크 활동이 있는 프로세스의 볼륨](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  cbengine.exe가 대용량 데이터를 전송하고 있지 않으면 다음 섹션의 단계를 완료합니다.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9단계: Azure Blob Storage에 대한 프로세스 서버 연결 확인

1. 리소스 모니터에서 **cbengine.exe**를 선택합니다.
2. **TCP 연결** 아래에서 프로세스 서버에서 Azure Storage로의 연결이 있는지 확인합니다.

  ![cbengine.exe와 Azure Blob Storage URL 간의 연결](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>서비스 확인

프로세스 서버에서 Azure Blob Storage URL로의 연결이 없으면 서비스가 실행 중인지 확인합니다.

1. 제어판에서 **서비스**를 선택합니다.
2. 다음 서비스가 실행 중인지 확인합니다.

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services 에이전트
    - Microsoft Azure Site Recovery 서비스
    - tmansvc

3. 실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다.
4. 프로세스 서버가 연결되어 있고 접근할 수 있는지 확인합니다. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10단계: Azure 공용 IP 주소에 대한 프로세스 서버 연결 확인

1. 프로세스 서버의 **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**에서 최신 CBEngineCurr.errlog 파일을 엽니다.
2. 파일에서 **443** 또는 문자열 **connection attempt failed**를 검색합니다.

  ![임시 폴더의 오류 로그](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 문제가 확인되면 포트 443을 사용하여 CBEngineCurr.currLog 파일에서 Azure 공용 IP 주소를 찾습니다.

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 프로세스 서버의 명령줄에서 텔넷을 사용하여 Azure 공용 IP 주소를 ping합니다.
6. 연결할 수 없는 경우 다음 절차를 수행합니다.

## <a name="step-11-check-process-server-firewall-settings"></a>11단계: 프로세스 서버 방화벽 설정을 확인합니다. 

프로세스 서버의 IP 주소 기반 방화벽이 액세스를 차단하는지를 확인합니다.

1. IP 주소 기반 방화벽 규칙의 경우:

    a) [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)의 전체 목록을 다운로드합니다.

    b) IP 주소 범위를 방화벽 구성에 추가하여 방화벽이 Azure(및 기본 HTTPS 포트 443)와의 통신을 허용하도록 합니다.

    c) 구독의 Azure 지역과 Azure 미국 서부 지역에 해당하는 IP 주소 범위를 허용합니다(액세스 제어 및 ID 관리에 사용됨).

2. URL 기반 방화벽의 경우 다음 표에 나열된 URL을 방화벽 구성에 추가합니다.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12단계: 프로세스 서버 프록시 설정 확인 

1. 프록시 서버를 사용하는 경우 DNS 서버에서 프록시 서버 이름을 확인하는지 확인합니다. 레지스트리 키 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**에서 구성 서버를 설정할 때 제공한 값을 확인합니다.
2. Azure Site Recovery 에이전트에서 데이터를 보내는 데 동일한 설정을 사용하는지 확인합니다.

    a) **Microsoft Azure Backup**을 검색합니다.

    b) **Microsoft Azure Backup**을 열고 **작업** > **속성 변경**을 선택합니다.

    c) **프록시 구성** 탭에서 프록시 주소는 레지스트리 설정에 표시된 프록시 주소와 동일해야 합니다. 그렇지 않은 경우 동일한 주소로 변경하세요.

## <a name="step-13-check-bandwidth"></a>13단계: 대역폭 확인

프로세스 서버와 Azure 간의 대역폭을 늘린 다음, 문제가 계속 발생하는지 확인합니다.


## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery의 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-site-recovery.html)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
