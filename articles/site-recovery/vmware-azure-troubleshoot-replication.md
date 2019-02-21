---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구에 대한 복제 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 중에 일반적인 복제 문제를 해결하는 방법을 제공합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/7/2019
ms.author: mayg
ms.openlocfilehash: 71c07d93d75ee372a50ec4ff5fc81e92926d329b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964783"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버에 대한 복제 문제 해결

Azure Site Recovery를 사용하여 VMware 가상 머신 또는 물리적 서버를 보호하는 경우 특정 오류 메시지가 나타날 수 있습니다. 이 문서에서는 [Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware VM 및 실제 서버를 Azure에 복제하는 경우 발생할 수 있는 몇 가지 일반적인 문제를 설명합니다.

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>복제 문제 방지를 위해 프로세스 서버 상태 모니터링

포털에서 PS(프로세스 서버) 상태를 모니터링하여 연결된 원본 머신에 대한 복제가 진행 중인지 확인하는 것이 좋습니다. 자격 증명 모음에서 관리 > Site Recovery 인프라 > 구성 서버로 이동합니다. 구성 서버 블레이드에서 연결된 서버 아래의 프로세스 서버를 클릭합니다. 상태 통계가 포함된 프로세스 서버 블레이드가 열립니다. 복제, 인증서 만료 날짜 및 사용 가능한 여유 공간에 필요한 PS 서비스의 CPU 활용률, 메모리 사용량, 상태를 추적할 수 있습니다. 모든 통계의 상태가 녹색이어야 합니다. 

**메모리 및 CPU 사용량을 70% 미만으로, 사용 가능한 공간을 25% 이상으로 유지하는 것이 좋습니다**. 사용 가능한 공간은 프로세스 서버의 캐시 디스크 공간을 가리키며, Azure로 업로드하기 전에 원본 머신의 복제 데이터를 저장하는 데 사용됩니다. 20% 미만으로 감소하면 모든 연결된 원본 머신에 대한 복제가 제한됩니다. [용량 지침](./site-recovery-plan-capacity-vmware.md#capacity-considerations)에 따라 원본 머신을 복제하는 데 필요한 구성을 파악합니다.

PS 머신에서 다음 서비스가 실행 중인지 확인합니다. 실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다.

**기본 제공 프로세스 서버**

* cxprocessserver
* InMage PushInstall
* Log Upload 서비스(LogUpload)
* InMage Scout Application 서비스
* Microsoft Azure Recovery Services 에이전트(obengine)
* InMage Scout VX Agent - Sentinel/Outpost(svagents)
* tmansvc
* World Wide Web Publishing 서비스(W3SVC)
* MySQL
* Microsoft Azure Site Recovery 서비스(dra)

**스케일 아웃 프로세스 서버**

* cxprocessserver
* InMage PushInstall
* Log Upload 서비스(LogUpload)
* InMage Scout Application 서비스
* Microsoft Azure Recovery Services 에이전트(obengine)
* InMage Scout VX Agent - Sentinel/Outpost(svagents)
* tmansvc

**장애 복구(failback)를 위한 Azure의 프로세스 서버**

* cxprocessserver
* InMage PushInstall
* Log Upload 서비스(LogUpload)

모든 서비스의 StartType이 **자동 또는 자동(지연된 시작)** 으로 설정되었는지 확인합니다. Microsoft Azure Recovery Services 에이전트(obengine) 서비스에는 위와 같은 StartType 설정이 필요하지 않습니다.

## <a name="initial-replication-issues"></a>초기 복제 문제

종종 원본 서버와 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 초기 복제가 실패할 수 있습니다. 대부분의 경우 다음 섹션의 단계를 완료하여 이러한 문제를 해결할 수 있습니다.

### <a name="check-the-source-machine"></a>원본 컴퓨터 확인

다음 목록은 원본 컴퓨터를 확인하는 방법입니다.

*  원본 서버의 명령줄에서 아래의 명령을 실행하여 텔넷으로 HTTPS 포트를 통해 프로세스 서버를 ping합니다. 프로세스 서버는 HTTPS 포트 9443을 기본적으로 사용하여 복제 트래픽을 주고받습니다. 등록 시 이 포트를 수정할 수 있습니다. 다음 명령은 네트워크 연결 문제와 방화벽 포트 차단 문제가 있는지 확인합니다.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Telnet을 사용하여 연결을 테스트합니다. `ping`은 사용하지 않도록 합니다. 텔넷을 설치하지 않은 경우 [텔넷 클라이언트 설치](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)에 나열된 단계를 완료합니다.

   텔넷이 PS 포트에 성공적으로 연결할 수 있는 경우 빈 화면이 표시됩니다.

   프로세스 서버에 연결할 수 없는 경우 프로세스 서버에서 인바운드 포트 9443을 허용합니다. 예를 들어, 네트워크에 경계 네트워크 또는 스크린된 서브넷이 있는 경우 프로세스 서버에서 인바운드 포트 9443을 허용해야 할 수 있습니다. 그런 다음, 문제가 여전히 발생하는지 확인합니다.

*  텔넷이 연결되고 원본 머신이 PS에 연결할 수 없다고 보고하는 경우 원본 머신에서 웹 브라우저를 열고 https://<PS_IP>: <PS_Data_Port>/ 주소에 연결할 수 있는지 확인합니다.

    이 주소에 도달하면 HTTPS 인증서 오류가 발생합니다. 인증서 오류를 무시하고 계속하면 400 - 잘못된 요청이 발생합니다. 이는 서버가 브라우저의 요청을 처리할 수 없으며 서버에 대한 표준 HTTPS 연결이 제대로 작동하고 정상 상태임을 의미합니다.

    실패하면 브라우저의 오류 메시지에 대한 세부 정보가 지침을 제공합니다. 예를 들어 프록시 인증이 잘못된 경우 프록시 서버에서 오류 메시지에 필요한 작업과 함께 407 - 프록시 인증을 반환합니다. 

*  네트워크 업로드 실패와 관련된 오류는 원본 VM에서 다음 로그를 참조하세요.

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server"></a>프로세스 서버 확인

다음 목록은 프로세스 서버를 확인하는 방법입니다.

> [!NOTE]
> 프로세스 서버에 고정 IPv4 주소가 있어야 하며 NAT IP가 구성되어 있으면 안 됩니다.

* **원본 머신과 프로세스 서버 간의 연결 확인**
1. 원본 머신에서 텔넷에 연결할 수 있지만 원본에서 PS에 연결할 수 없는 경우 원본 VM에서 cxpsclient 도구를 실행하여 원본 VM의 cxprocessserver와 엔드투엔드 연결을 확인합니다.

       <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

    해당 오류에 대한 자세한 내용은 다음 디렉터리에서 PS에 대해 생성된 로그를 참조하세요.

       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
       and
       C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
2. PS의 하트비트가 없는 경우 PS에 대한 다음 로그를 참조하세요.

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

*  **프로세스 서버가 데이터를 Azure로 적극적으로 밀어넣는지 확인합니다**.

   1. 프로세스 서버에서 작업 관리자를 엽니다(Ctrl+Shift+Esc 누르기).
   2. **성능** 탭을 선택하고 **리소스 모니터 열기** 링크를 선택합니다. 
   3. **리소스 모니터** 페이지에서 **네트워크** 탭을 선택합니다. **네트워크 활동이 있는 프로세스**에서 **cbengine.exe**가 현재 대용량 데이터를 전송하고 있는지 확인합니다.

        ![네트워크 활동이 있는 프로세스 아래에 볼륨을 표시하는 스크린샷](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   cbengine.exe가 대용량 데이터를 전송하고 있지 않으면 다음 섹션의 단계를 완료합니다.

*  **프로세스 서버가 Azure Blob 스토리지에 연결할 수 있는지 여부를 확인합니다**.

   **cbengine.exe**를 선택합니다. **TCP 연결** 아래에서 프로세스 서버로부터 Azure Blob 스토리지 URL로의 연결이 있는지 확인합니다.

   ![cbengine.exe와 Azure Blob 스토리지 URL 간의 연결을 보여 주는 스크린샷](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   프로세스 서버로부터 Azure Blob 스토리지 URL로의 연결이 없으면 제어판에서 **서비스**를 선택합니다. 다음 서비스가 실행 중인지 여부를 확인합니다.

   *  cxprocessserver
   *  InMage Scout VX Agent – Sentinel/Outpost
   *  Microsoft Azure Recovery Services 에이전트
   *  Microsoft Azure Site Recovery 서비스
   *  tmansvc

   실행되지 않고 있는 모든 서비스를 시작하거나 다시 시작합니다. 문제가 여전히 발생하는지 여부를 확인합니다.

*  **프로세스 서버가 포트 443을 사용하여 Azure 공용 IP 주소에 연결할 수 있는지 여부를 확인합니다**.

   %programfiles%\Microsoft Azure Recovery Services Agent\Temp에서 최신 CBEngineCurr.errlog 파일을 엽니다. 파일에서 **443** 또는 문자열 **connection attempt failed**를 검색합니다.

   ![Temp 폴더의 로그 오류를 표시하는 스크린샷](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   문제가 표시되면 프로세스 서버의 명령줄에서 Telnet을 사용하여 Azure 공용 IP 주소(이전 이미지에서는 IP 주소가 마스킹됨)를 ping합니다. 포트 443을 사용하여 CBEngineCurr.currLog 파일에서 Azure 공용 IP 주소를 찾을 수 있습니다.

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   연결할 수 없으면 다음 단계에 설명된 대로 액세스 문제가 방화벽 또는 프록시 설정으로 인한 것인지 확인합니다.

*  **프로세스 서버의 IP 주소 기반 방화벽이 액세스를 차단하는지 여부를 확인합니다**.

   서버에서 IP 주소 기반 방화벽 규칙을 사용하는 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)의 전체 목록을 다운로드합니다. IP 주소 범위를 방화벽 구성에 추가하여 방화벽이 Azure(및 기본 HTTPS 포트 443)와의 통신을 허용하도록 합니다. 구독하는 Azure 지역과 Azure 미국 서부 지역에 해당하는 IP 주소 범위를 허용합니다(액세스 제어 및 ID 관리에 사용됨).

*  **프로세스 서버의 URL 기반 방화벽이 액세스를 차단하는지 여부를 확인합니다**.

   서버에서 URL 기반 방화벽 규칙을 사용하는 경우 다음 표에 나열된 URL을 방화벽 구성에 추가합니다.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **프로세스 서버의 프록시 설정이 액세스를 차단할지 여부를 확인합니다**.

   프록시 서버를 사용하는 경우 DNS 서버에서 프록시 서버 이름을 확인하는지 확인합니다. 구성 서버를 설정할 때 제공한 값을 확인하려면 레지스트리 키 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**로 이동합니다.

   다음으로, Azure Site Recovery에서 데이터를 보내는 데 동일한 설정을 사용하는지 확인합니다. 
      
   1. **Microsoft Azure Backup**을 검색합니다. 
   2. **Microsoft Azure Backup**을 열고 **작업** > **속성 변경**을 선택합니다. 
   3. **프록시 구성** 탭에 프록시 주소가 표시되어야 합니다. 프록시 주소는 레지스트리 설정에 표시된 프록시 주소와 동일해야 합니다. 그렇지 않은 경우 동일한 주소로 변경하세요.

*  **프로세스 서버에서 스로틀 대역폭이 제한되는지 여부를 확인합니다**.

   대역폭을 늘리고 문제가 여전히 발생하는지 확인합니다.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>원본 컴퓨터가 Azure Portal에 나열되지 않음

Site Recovery를 사용하여 복제를 사용하도록 설정할 원본 컴퓨터를 선택하려고 하면 다음과 같은 이유 중 하나로 컴퓨터를 계속 사용하지 못할 수 있습니다.

* **인스턴스 UUID가 같은 두 개의 가상 머신**: vCenter에 인스턴스 UUID가 같은 두 개의 가상 머신이 있는 경우 구성 서버에서 첫 번째로 검색된 가상 머신이 Azure Portal에 표시됩니다. 이 문제를 해결하려면 두 개의 가상 머신에 동일한 인스턴스 UUID가 없는지 확인합니다. 이 시나리오는 백업 VM이 활성화되고 검색 레코드에 기록되는 경우에 일반적으로 발생합니다. [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.
* **잘못된 vCenter 사용자 자격 증명**: OVF 템플릿 또는 통합 설정을 사용하여 구성 서버를 설정하는 동안 올바른 vCenter 자격 증명을 추가했는지 확인합니다. 설치 중에 추가한 자격 증명을 확인하려면 [자동 검색에 대한 자격 증명 수정](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)을 참조하세요.
* **vCenter 권한 부족**: vCenter에 액세스하기 위해 제공된 권한에 필요한 사용 권한이 없으면 가상 머신을 검색하지 못할 수 있습니다. [자동 검색용 계정 준비](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)에 설명된 사용 권한을 vCenter 사용자 계정에 추가해야 합니다.
* **Azure Site Recovery 관리 서버**: 구성 서버/스케일 아웃 프로세스 서버/마스터 대상 서버 역할 중 하나 이상에서 가상 머신이 관리 서버로 사용되는 경우 포털에서 가상 머신을 선택할 수 없습니다. 관리 서버는 복제할 수 없습니다.
* **Azure Site Recovery 서비스를 통해 이미 보호/장애 조치(Failover)됨**: Site Recovery를 통해 가상 머신을 이미 보호 또는 장애 조치하는 경우 포털에서 보호하기 위해 가상 머신을 선택할 수 없습니다. 포털에서 찾고 있는 가상 머신이 다른 사용자 또는 다른 구독을 통해 아직 보호되고 있지 않은지 확인합니다.
* **연결되지 않은 vCenter** VCenter가 연결된 상태인지 확인합니다. 확인하려면 [Recovery Services 가격 증명 모음] > [Site Recovery 인프라] > [구성 서버]로 이동하고 > 해당 구성 서버를 클릭하면 > 연결된 서버의 세부 정보와 함께 블레이드가 오른쪽에 열립니다. VCenter가 연결되었는지 확인합니다. “연결되지 않음” 상태인 경우 문제를 해결한 다음, 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **ESXi 전원 꺼짐**: 가상 머신이 상주하는 ESXi 호스트의 전원이 꺼진 상태이면 가상 머신이 Azure Portal에 나열되지 않거나 선택할 수 없습니다. ESXi 호스트 전원을 켜고 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **보류 중인 재부팅**: 가상 머신에 보류 중인 재부팅이 있는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 보류 중인 재부팅 활동을 완료하고 [구성 서버를 새로 고쳐야 합니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **IP 찾을 수 없음**: 가상 머신에 연결된 유효한 IP 주소가 없는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 가상 머신에 유효한 IP 주소를 할당하고 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>보호된 가상 머신은 포털에 회색으로 표시됩니다.

Site Recovery에서 복제된 가상 머신은 시스템에 중복된 항목이 있으면 Azure Portal에서 사용할 수 없습니다. 오래된 항목을 삭제하고 이 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 활발히 유지되는 커뮤니티가 있으며 엔지니어 중 하나가 도움을 줄 수 있습니다.
