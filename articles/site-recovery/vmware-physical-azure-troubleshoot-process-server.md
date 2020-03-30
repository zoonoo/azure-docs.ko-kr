---
title: Azure 사이트 복구 프로세스 서버 문제 해결
description: 이 문서에서는 Azure 사이트 복구 프로세스 서버의 문제 해결 방법을 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256862"
---
# <a name="troubleshoot-the-process-server"></a>프로세스 서버 문제 해결

[사이트 복구](site-recovery-overview.md) 프로세스 서버는 온-프레미스 VM웨어 VM 및 물리적 서버에 대한 재해 복구를 Azure에 설정할 때 사용됩니다. 이 문서에서는 복제 및 연결 문제를 포함하여 프로세스 서버의 문제를 해결하는 방법에 대해 설명합니다.

프로세스 서버에 대해 [자세히 알아보세요.](vmware-physical-azure-config-process-server-overview.md)

## <a name="before-you-start"></a>시작하기 전에

문제 해결을 시작하기 전에 다음을 수행하십시오.

1. [프로세스 서버를 모니터링하는](vmware-physical-azure-monitor-process-server.md)방법을 이해해야 합니다.
2. 아래의 모범 사례를 검토하십시오.
3. 용량 고려 [사항을](site-recovery-plan-capacity-vmware.md#capacity-considerations)따르고 [구성 서버](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) 또는 독립 실행형 프로세스 서버에 대한 크기 조정 지침을 사용해야 [합니다.](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)

## <a name="best-practices-for-process-server-deployment"></a>프로세스 서버 배포에 대한 모범 사례

프로세스 서버의 최적의 성능을 위해 여러 가지 일반적인 모범 사례를 요약했습니다.

**모범 사례** | **세부 정보**
--- |---
**사용** | 구성 서버/독립 실행형 프로세스 서버가 의도한 용도로만 사용되는지 확인합니다. 컴퓨터에서 다른 것을 실행하지 마십시오.
**IP 주소** | 프로세스 서버에 정적 IPv4 주소가 있고 NAT가 구성되어 있지 않은지 확인합니다.
**메모리/CPU 사용량 제어** |CPU 및 메모리 사용량을 70% 미만으로 유지합니다.
**여유 공간 보장** | 여유 공간은 프로세스 서버의 캐시 디스크 공간을 나타냅니다. 복제 데이터는 Azure에 업로드되기 전에 캐시에 저장됩니다.<br/><br/> 여유 공간을 25% 이상 유지합니다. 20% 미만으로 떨어지면 프로세스 서버와 연결된 복제 된 컴퓨터에 대한 복제가 제한됩니다.

## <a name="check-process-server-health"></a>프로세스 서버 상태 확인

문제 해결의 첫 번째 단계는 프로세스 서버의 상태와 상태를 확인하는 것입니다. 이렇게 하려면 모든 경고를 검토하고 필요한 서비스가 실행 중인지 확인하고 프로세스 서버에서 하트비트가 있는지 확인합니다. 다음 그래픽에 이러한 단계가 요약되어 있으며 단계를 수행하는 데 도움이 되는 절차가 표시됩니다.

![프로세스 서버 상태 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>1단계: 프로세스 서버 상태 경고 문제 해결

프로세스 서버는 여러 가지 상태 경고를 생성합니다. 이러한 경고 및 권장 작업은 다음 표에 요약되어 있습니다.

**경고 유형** | **오류** | **문제 해결**
--- | --- | --- 
![Healthy][green] | None  | 프로세스 서버가 연결되고 정상입니다.
![Warning][yellow] | 지정된 서비스가 실행되고 있지 않습니다. | 1. 서비스가 실행 중이지 확인합니다.<br/> 2. 서비스가 예상대로 실행되는 경우 아래 지침에 따라 [연결 및 복제 문제를 해결하십시오.](#check-connectivity-and-replication)
![Warning][yellow]  | CPU 사용률은 지난 15분 동안 80%>. | 1. 새 컴퓨터를 추가하지 마십시오.<br/>2. 프로세스 서버를 사용하는 VM 수가 [정의된 제한에](site-recovery-plan-capacity-vmware.md#capacity-considerations)부합하는지 확인하고 [추가 프로세스 서버를](vmware-azure-set-up-process-server-scale.md)설정하는 것이 좋습니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제를 해결합니다.](#check-connectivity-and-replication)
![위험][red] |  CPU 사용률은 지난 15분 동안 95% >. | 1. 새 컴퓨터를 추가하지 마십시오.<br/>2. 프로세스 서버를 사용하는 VM 수가 [정의된 제한에](site-recovery-plan-capacity-vmware.md#capacity-considerations)부합하는지 확인하고 [추가 프로세스 서버를](vmware-azure-set-up-process-server-scale.md)설정하는 것이 좋습니다.<br/>3. 아래 지침에 따라 [연결 및 복제 문제를 해결합니다.](#check-connectivity-and-replication)<br/> 4. 문제가 지속되면 VMware/물리적 서버 복제를 위한 [배포 플래너를](https://aka.ms/asr-v2a-deployment-planner) 실행합니다.
![Warning][yellow] | 메모리 사용량은 지난 15분 동안 80%>. |  1. 새 컴퓨터를 추가하지 마십시오.<br/>2. 프로세스 서버를 사용하는 VM 수가 [정의된 제한에](site-recovery-plan-capacity-vmware.md#capacity-considerations)부합하는지 확인하고 [추가 프로세스 서버를](vmware-azure-set-up-process-server-scale.md)설정하는 것이 좋습니다.<br/>3. 경고와 관련된 지침을 따르십시오.<br/> 4. 문제가 지속되면 아래 지침에 따라 [연결 및 복제 문제를 해결합니다.](#check-connectivity-and-replication)
![위험][red] | 메모리 사용량은 지난 15분 동안 95%>. | 1. 새 컴퓨터를 추가하지 말고 추가 프로세스 서버를 설정하는 것을 [고려합니다.](vmware-azure-set-up-process-server-scale.md)<br/> 2. 경고와 관련된 지침을 따르십시오.<br/> 3. 4. 문제가 계속되면 아래 지침에 따라 [연결 및 복제 문제를 해결합니다.](#check-connectivity-and-replication)<br/> 4. 문제가 지속되면 VMware/물리적 서버 복제 문제에 대한 [배포 플래너를](https://aka.ms/asr-v2a-deployment-planner) 실행합니다.
![Warning][yellow] | 캐시 폴더 여유 공간은 지난 15분 동안 30% <. | 1. 새 컴퓨터를 추가하지 말고 추가 [프로세스 서버를](vmware-azure-set-up-process-server-scale.md)설정하는 것이 좋습니다.<br/>2. 프로세스 서버를 사용하는 VM 수가 [지침에](site-recovery-plan-capacity-vmware.md#capacity-considerations)맞는지 확인합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제를 해결합니다.](#check-connectivity-and-replication)
![위험][red] |  지난 15분 동안 25% < 여유 공간 | 1. 이 문제에 대한 경고와 관련된 지침을 따르십시오.<br/> 2. 3. [연결 및 복제 문제를 해결하려면](#check-connectivity-and-replication)아래 지침을 따르십시오.<br/> 3. 문제가 지속되면 VMware/물리적 서버 복제를 위한 [배포 플래너를](https://aka.ms/asr-v2a-deployment-planner) 실행합니다.
![위험][red] | 15분 이상 프로세스 서버에서 하트비트가 없습니다. tmansvs 서비스가 구성 서버와 통신하지 않습니다. | 1) 프로세스 서버가 실행 중이고 있는지 확인합니다.<br/> 2. tmassvc가 프로세스 서버에서 실행되고 있는지 확인합니다.<br/> 3. 아래 지침에 따라 [연결 및 복제 문제를 해결합니다.](#check-connectivity-and-replication)


![테이블 키](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>2단계: 프로세스 서버 서비스 확인

프로세스 서버에서 실행해야 하는 서비스는 다음 표에 요약되어 있습니다. 프로세스 서버를 배포하는 방법에 따라 서비스에 약간의 차이가 있습니다. 

Microsoft Azure 복구 서비스 에이전트(obengine)를 제외한 모든 서비스의 경우 StartType이 **자동** 또는 **자동(지연된 시작)으로**설정되어 있는지 확인합니다.
 
**배포** | **서비스 실행**
--- | ---
**구성 서버의 프로세스 서버** | 프로세스 서버; 프로세스 서버 모니터; cxprocessserver; 인매지 푸시설치; 로그 업로드 서비스(로그 업로드); 인마지 스카우트 응용 프로그램 서비스; 마이크로 소프트 Azure 복구 서비스 에이전트 (obengine); 인매지 스카우트 VX 에이전트 - 센티넬 / 전초 기지 (스브 에이전트); tmansvc; 월드 와이드 웹 게시 서비스(W3SVC); MySQL; 마이크로소프트 Azure 사이트 복구 서비스 (드라)
**독립 실행형 서버로 실행되는 프로세스 서버** | 프로세스 서버; 프로세스 서버 모니터; cxprocessserver; 인매지 푸시설치; 로그 업로드 서비스(로그 업로드); 인마지 스카우트 응용 프로그램 서비스; 마이크로 소프트 Azure 복구 서비스 에이전트 (obengine); 인매지 스카우트 VX 에이전트 - 센티넬 / 전초 기지 (스브 에이전트); tmansvc.
**장애 복구를 위해 Azure에 배포된 프로세스 서버** | 프로세스 서버; 프로세스 서버 모니터; cxprocessserver; 인매지 푸시설치; 로그 업로드 서비스(로그 업로드)


## <a name="step-3-check-the-process-server-heartbeat"></a>3단계: 프로세스 서버 하트비트 확인

프로세스 서버(오류 코드 806)의 하트비트가 없는 경우 다음을 수행합니다.

1. 프로세스 서버 VM이 실행 되고 있는지 확인합니다.
2. 이러한 로그에서 오류가 있는지 확인합니다.

    C:\프로그램데이터\ASR\홈\svsystems\이벤트 관리자*\로그 C\ProgramData\ASR\홈\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>연결 및 복제 확인

 초기 및 진행 중인 복제 오류는 원본 컴퓨터와 프로세스 서버 간의 연결 문제 또는 프로세스 서버와 Azure 간에 발생하는 경우가 많습니다. 다음 그래픽에 이러한 단계가 요약되어 있으며 단계를 수행하는 데 도움이 되는 절차가 표시됩니다.

![연결 및 복제 문제 해결](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>4단계: 소스 컴퓨터에서 시간 동기화 확인

복제된 컴퓨터의 시스템 날짜/시간이 동기화되어 있는지 확인합니다. [자세히 알아보기](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>5 단계: 소스 컴퓨터에서 바이러스 백신 소프트웨어 확인

복제된 컴퓨터에서 바이러스 백신 소프트웨어가 사이트 복구를 차단하지 않는지 확인합니다. 바이러스 백신 프로그램에서 사이트 복구를 제외해야 하는 경우 [이 문서를 검토하십시오.](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)

## <a name="step-6-check-connectivity-from-source-machine"></a>6단계: 소스 컴퓨터에서 연결 확인


1. 필요한 경우 소스 컴퓨터에 [Telnet 클라이언트를](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) 설치합니다. 핑을 사용하지 마십시오.
2. 원본 컴퓨터에서 Telnet을 통해 HTTPS 포트에서 프로세스 서버를 ping합니다. 기본적으로 9443은 복제 트래픽에 대한 HTTPS 포트입니다.

    `telnet <process server IP address> <port>`

3. 연결이 성공했는지 확인합니다.


**연결** | **세부 정보** | **작업**
--- | --- | ---
**성공적인** | 텔넷은 빈 화면을 표시하고 프로세스 서버에 연결할 수 있습니다. | 추가 작업이 필요하지 않습니다.
**실패** | 연결할 수 없습니다. | 인바운드 포트 9443이 프로세스 서버에서 허용되는지 확인합니다. 예를 들어 경계 네트워크 또는 스크린서브넷이 있는 경우입니다. 연결을 다시 확인합니다.
**부분적으로 성공** | 연결할 수는 있지만 원본 컴퓨터에서는 프로세스 서버에 연결할 수 없다고 보고합니다. | 다음 문제 해결 절차를 계속합니다.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>7단계: 연결할 수 없는 프로세스 서버 문제 해결

프로세스 서버가 원본 컴퓨터에서 연결할 수 없는 경우 오류 78186이 표시됩니다. 해결되지 않으면 이 문제가 앱 일관성 및 충돌 일관된 복구 지점이 예상대로 생성되지 않도록 합니다.

원본 컴퓨터가 프로세스 서버의 IP 주소에 도달할 수 있는지 확인하고 소스 컴퓨터에서 cxpsclient 도구를 실행하여 종단 간 연결을 확인하여 문제를 해결합니다.


### <a name="check-the-ip-connection-on-the-process-server"></a>프로세스 서버에서 IP 연결 확인

텔넷이 성공했지만 원본 컴퓨터에서 프로세스 서버에 연결할 수 없다고 보고하는 경우 프로세스 서버의 IP 주소에 도달할 수 있는지 확인합니다.

1. 웹 브라우저에서 ip 주소 https://<PS_IP>:<PS_Data_Port>/<<도달하려고 합니다.
2. 이 검사에 HTTPS 인증서 오류가 표시되면 정상입니다. 오류를 무시하면 400 - 잘못된 요청이 표시됩니다. 즉, 서버가 브라우저 요청을 제공할 수 없으며 표준 HTTPS 연결이 괜찮습니다.
3. 이 검사가 작동하지 않으면 브라우저 오류 메시지를 기록합니다. 예를 들어 407 오류는 프록시 인증문제를 나타냅니다.

### <a name="check-the-connection-with-cxpsclient"></a>cxpsclient와의 연결 확인

또한 cxpsclient 도구를 실행하여 종단 간 연결을 확인할 수 있습니다.

1. 다음과 같이 도구를 실행합니다.

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. 프로세스 서버에서 다음 폴더에서 생성된 로그를 확인합니다.

    C:\프로그램데이터\ASR\홈\svsystems\전송\log\cxps.err C:\ProgramData\ASR\home\svsystems\전송\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>업로드 실패에 대한 소스 VM 로그 확인(오류 78028)

원본 컴퓨터에서 프로세스 서비스로 차단된 데이터 업로드에 문제가 발생하면 충돌 일관성과 앱 일관된 복구 지점이 모두 생성되지 않을 수 있습니다. 

1. 네트워크 업로드 실패문제를 해결하려면 이 로그에서 오류를 찾을 수 있습니다.

    C:\프로그램 파일(x86)\마이크로소프트 Azure 사이트 복구\에이전트\svagents*.log 

2. 이 문서의 나머지 절차를 사용하면 데이터 업로드 문제를 해결하는 데 도움이 될 수 있습니다.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>8단계: 프로세스 서버가 데이터를 푸시하고 있는지 확인

프로세스 서버가 데이터를 Azure로 적극적으로 밀어넣는지 확인합니다.

  1. 프로세스 서버에서 작업 관리자를 엽니다(Ctrl+Shift+Esc 누르기).
  2. **리소스 모니터 열기**> **성능** 탭을 선택합니다.
  3. **리소스 모니터** 페이지에서 **네트워크** 탭을 선택합니다. **네트워크 활동이 있는 프로세스에서**cbengine.exe가 대량의 데이터를 적극적으로 전송하고 있는지 확인합니다.

       ![네트워크 활동이 있는 프로세스 중인 볼륨](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  cbengine.exe가 대용량 데이터를 전송하고 있지 않으면 다음 섹션의 단계를 완료합니다.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>9단계: Azure Blob 저장소에 대한 프로세스 서버 연결 확인

1. 리소스 모니터에서 **cbengine.exe를**선택합니다.
2. **TCP 연결에서**프로세스 서버에서 Azure 저장소에 대한 연결이 있는지 확인합니다.

  ![cbengine.exe와 Azure Blob 저장소 URL 간의 연결](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>서비스 확인

프로세스 서버에서 Azure Blob 저장소 URL에 대한 연결이 없는 경우 서비스가 실행 중인지 확인합니다.

1. 제어판에서 **서비스를**선택합니다.
2. 다음 서비스가 실행 되고 있는지 확인합니다.

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services 에이전트
    - Microsoft Azure Site Recovery 서비스
    - tmansvc

3. 실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다.
4. 프로세스 서버가 연결되어 있고 연결할 수 있는지 확인합니다. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>10단계: Azure 공용 IP 주소에 대한 프로세스 서버 연결 확인

1. 프로세스 서버에서 **%programfiles%\Microsoft Azure 복구 서비스 에이전트\Temp에서**최신 CBEngineCurr.errlog 파일을 엽니다.
2. 파일에서 **443**을 검색하거나 문자열 **연결 시도가 실패했습니다.**

  ![임시 폴더의 오류 로그](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. 문제가 발생하면 포트 443을 사용하여 CBEngineCurr.currLog 파일에 Azure 공용 IP 주소를 위치합니다.

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. 프로세스 서버의 명령줄에서 Telnet을 사용하여 Azure 공용 IP 주소를 ping합니다.
6. 연결할 수 없는 경우 다음 절차를 따르십시오.

## <a name="step-11-check-process-server-firewall-settings"></a>11단계: 프로세스 서버 방화벽 설정을 확인합니다. 

프로세스 서버의 IP 주소 기반 방화벽이 액세스를 차단하고 있는지 확인합니다.

1. IP 주소 기반 방화벽 규칙의 경우:

    a) Microsoft Azure [데이터 센터 IP 범위의](https://www.microsoft.com/download/details.aspx?id=41653)전체 목록을 다운로드합니다.

    b) 방화벽이 Azure(및 기본 HTTPS 포트, 443)에 통신할 수 있도록 방화벽 구성에 IP 주소 범위를 추가합니다.

    c) 구독의 Azure 지역 및 Azure West 미국 리전(액세스 제어 및 ID 관리에 사용)에 대한 IP 주소 범위 허용

2. URL 기반 방화벽의 경우 다음 표에 나열된 URL을 방화벽 구성에 추가합니다.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>12단계: 프로세스 서버 프록시 설정 확인 

1. 프록시 서버를 사용하는 경우 DNS 서버에서 프록시 서버 이름을 확인하는지 확인합니다. 레지스트리 키 **HKEY_LOCAL_MACHINE[SOFTWARE\Microsoft\Azure 사이트 복구\프록시설정]에서**구성 서버를 설정할 때 제공한 값을 확인합니다.
2. Azure 사이트 복구 에이전트에서 동일한 설정을 사용하여 데이터를 전송해야 합니다.

    a) **마이크로 소프트 Azure 백업에**대한 검색 .

    b) **Microsoft Azure 백업을**열고 **작업** > **변경 속성을**선택합니다.

    c) **프록시 구성** 탭에서 프록시 주소는 레지스트리 설정에 표시된 프록시 주소와 같아야 합니다. 그렇지 않은 경우 동일한 주소로 변경하세요.

## <a name="step-13-check-bandwidth"></a>13단계: 대역폭 확인

프로세스 서버와 Azure 간의 대역폭을 늘인 다음 문제가 여전히 발생하는지 확인합니다.


## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
