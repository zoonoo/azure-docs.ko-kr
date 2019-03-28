---
title: Azure Backup을 사용하여 Recovery Services 자격 증명 모음의 Azure VM 백업
description: Azure Backup을 사용하여 Recovery Services 자격 증명 모음의 Azure VM을 백업하는 방법을 설명합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: 7fc724f1aff40c6dedff59ce3919496a30b30337
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520176"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 Azure VM 백업

이 문서에서는 사용 하 여 Recovery Services 자격 증명 모음에 Azure Vm을 백업 하는 방법을 설명 합니다 [Azure Backup](backup-overview.md) 서비스입니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 지원 및 백업에 대 한 필수 구성 요소를 확인 합니다.
> * Azure VM을 준비합니다. 필요한 경우 Azure VM 에이전트를 설치하고 VM에 대한 아웃바운드 액세스를 확인합니다.
> * 자격 증명 모음을 만듭니다.
> * Vm을 검색 하 고 백업 정책을 구성 합니다.
> * Azure Vm에 대 한 backup을 사용 합니다.


> [!NOTE]
   > 이 문서에서는 자격 증명 모음을 설정 하 고 백업할 Vm을 선택 하는 방법을 설명 합니다. 여러 VM을 백업하는 경우에 유용합니다. 또는 [단일 Azure VM 백업](backup-azure-vms-first-look-arm.md) VM 설정에서 직접.

## <a name="before-you-start"></a>시작하기 전에


- Azure VM 백업 아키텍처를 [검토](backup-architecture.md#architecture-direct-backup-of-azure-vms)합니다.
- Azure VM 백업 및 백업 확장에 대해 [자세히 알아봅니다](backup-azure-vms-introduction.md).
- Azure VM 백업에 대한 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.


## <a name="prepare-azure-vms"></a>Azure VM 준비

일부 경우에 Azure Vm에서 Azure VM 에이전트를 설정 하거나 명시적으로 VM에서 아웃 바운드 액세스를 허용 해야 합니다.

### <a name="install-the-vm-agent"></a>VM 에이전트 설치 

Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. VM을 만든 경우 Azure marketplace 이미지에서 에이전트를 설치 하 고 실행. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션한 경우에 표에 요약 된 대로 수동으로 에이전트를 설치 하는 것이 해야 합니다.

**VM** | **세부 정보**
--- | ---
**Windows VM** | 1. 에이전트 MSI 파일을 [다운로드하여 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다.<br/><br/> 2. 컴퓨터의 관리자 권한으로 설치합니다.<br/><br/> 3. 설치를 확인 합니다. *C:\WindowsAzure\Packages* VM에서 마우스 오른쪽 단추로 클릭는 WaAppAgent.exe > **속성**, > **세부 정보** 탭 합니다. **제품 버전**은 2.6.1198.718 이상이어야 합니다.<br/><br/> 에이전트를 업데이트하는 경우 백업 작업이 실행되고 있지 않은지 확인하고 [에이전트를 다시 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다.
**Linux VM** | 배포 패키지 리포지토리에서 RPM 또는 DEB 패키지를 사용 하 여 설치 합니다. 이 방법은 설치 및 Azure Linux 에이전트 업그레이드에 대 한 기본 설정 합니다. 모든 [인증 배포 공급자](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)는 이미지 및 리포지토리에 Azure Linux 에이전트 패키지를 통합합니다. 에이전트는 [GitHub](https://github.com/Azure/WALinuxAgent)에서 사용할 수 있지만 설치하지 않는 것이 좋습니다.<br/><br/> 에이전트를 업데이트 하는 경우 백업 작업이 실행 되 고 업데이트 이진 파일에 있는지 확인 합니다.


### <a name="establish-network-connectivity"></a>네트워크 연결 설정

VM에서 실행 중인 Backup 확장이 Azure 공용 IP 주소에 아웃 바운드 액세스를 해야 합니다.

- 일반적으로 명시적으로 Azure Backup을 사용 하 여 통신을 위해에서 Azure VM에 대 한 아웃 바운드 네트워크 액세스를 허용 하도록 필요가 없습니다.
- 실행에 어려움이 있을 vm에 연결 하 고 오류를 표시 하는 경우 **ExtensionSnapshotFailedNoNetwork** 연결을 시도할 때 명시적으로 허용 해야 액세스 백업 확장이 Azure 공용 네트워크와 통신할 수 있도록 백업 트래픽에 대 한 IP 주소입니다.


#### <a name="explicitly-allow-outbound-access"></a>명시적으로 아웃 바운드 액세스 허용

VM Backup 서비스에 연결할 수 없는 경우에 명시적으로 표에 요약 된 방법 중 하나를 사용 하 여 아웃 바운드 액세스를 허용 합니다.

**옵션** | **작업** | **세부 정보** 
--- | --- | --- 
**NSG 규칙 설정** | [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 허용합니다. | 허용 및 모든 주소 범위 관리를 대신 사용 하 여 Azure Backup 서비스에 대 한 액세스를 허용 하는 규칙을 추가할 수 있습니다는 [서비스 태그](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)합니다. [자세히 알아보기](../virtual-network/security-overview.md#service-tags).<br/><br/> 추가 비용 없음<br/><br/> 서비스 태그를 사용 하 여 관리를 간단 합니다.
**프록시 배포** | 트래픽 라우팅을 위해 HTTP 프록시 서버를 배포합니다. | 저장소뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다.<br/><br/> 스토리지 URL에 대한 세분화된 제어가 허용됩니다.<br/><br/> VM에 대한 인터넷 액세스의 단일 지점입니다.<br/><br/> 프록시에 대한 추가 비용이 없습니다.
**Azure Firewall 설정** | Azure Backup 서비스에 대한 FQDN 태그를 사용하여 VM에서 Azure Firewall을 통해 트래픽을 허용합니다. |  VNet 서브넷에서 Azure Firewall 설정이 있는 경우 간편하게 사용합니다.<br/><br/> 사용자 고유의 FQDN 태그를 만들거나 태그에 Fqdn을 수정할 수 없습니다.<br/><br/> Azure Managed Disks를 사용하는 경우 방화벽에서 열려 있는 추가 포트(포트 8443)가 필요할 수 있습니다.

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Azure에 아웃바운드 액세스를 허용하도록 NSG 규칙 설정

NSG에서 VM 액세스를 관리 되는 경우는 백업 저장소에 필요한 범위 및 포트에 아웃 바운드 액세스를 허용 합니다.

1. VM 속성에서 > **네트워킹**, 클릭 **아웃 바운드 포트 규칙 추가**합니다.
2. **아웃바운드 보안 규칙 추가**에서 **고급**을 클릭합니다.
3. **원본**에서 **VirtualNetwork**를 선택합니다.
4. **원본 포트 범위**에서 별표(*)를 입력하여 모든 포트에 아웃바운드 액세스를 허용합니다.
5. **대상**에서 **서비스 태그**를 선택합니다. 목록에서 **Storage.region**을 선택합니다. 지역은 자격 증명 모음이 있고 백업하려는 VM이 위치하는 지역입니다.
6. **대상 포트 범위**에서 포트를 선택합니다.
    - 암호화되지 않은 스토리지 계정을 사용하는 비관리형 VM: 80
    - 암호화된 스토리지 계정을 사용하는 비관리형 VM: 443(기본 설정)
    - 관리형 VM: 8443
7. **프로토콜**에서 **TCP**를 선택합니다.
8. **우선 순위**에서 모든 거부 규칙보다 더 작은 우선 순위 값을 지정합니다.
   - 액세스를 거부하는 규칙이 있는 경우 새 허용 규칙은 더 높아야 합니다.
   - 예를 들어 우선 순위 1000에서 **Deny_All** 규칙이 있는 경우 새 규칙은 1000 미만으로 설정해야 합니다.
9. 규칙에 대한 이름과 설명을 입력하고 **OK**를 클릭합니다.

아웃바운드 액세스를 허용하는 여러 VM에 NSG 규칙을 적용할 수 있습니다. 이 비디오에서는 프로세스에 대해 설명합니다.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


##### <a name="route-backup-traffic-through-a-proxy"></a>프록시를 통해 백업 트래픽 라우팅

프록시를 통해 백업 트래픽을 라우팅한 다음, 필요한 Azure 범위에 액세스할 수 있는 권한을 프록시에 부여할 수 있습니다. 프록시는 다음을 허용 하도록 VM을 구성 합니다.

- Azure VM은 프록시를 통해 공용 인터넷으로 향하는 모든 HTTP 트래픽을 라우팅해야 합니다.
- 프록시는 해당 가상 네트워크(VNet)의 VM에서 들어오는 트래픽을 허용해야 합니다.
- NSG **NSF-lockdown**은 프록시 VM에서 들어오는 아웃바운드 인터넷 트래픽을 허용하는 규칙이 필요합니다.

###### <a name="set-up-the-proxy"></a>프록시 설정

시스템 계정 프록시가 없는 경우 다음과 같이 설정합니다.

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553)을 다운로드합니다.
2. **PsExec.exe-i-s cmd.exe**를 실행하여 시스템 계정에서 명령 프롬프트를 실행합니다.
3. 시스템 컨텍스트에서 브라우저를 실행합니다. 예: Internet Explorer의 경우 **%PROGRAMFILES%\Internet Explorer\iexplore.exe**  
4. 프록시 설정을 정의합니다.
   - Linux 머신:
     - 이 줄을 **/etc/environment** 파일에 추가합니다.
       - **http_proxy = http: \/ /프록시 IP 주소: 프록시 포트**
     - 이 줄을 **/etc/waagent.conf** 파일에 추가합니다.
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - Windows 머신의 브라우저 설정에서 프록시를 사용함으로 지정합니다. 현재 사용자 계정에서 프록시를 사용하는 경우 시스템 계정 수준에서 설정을 적용하려면 이 스크립트를 사용할 수 있습니다.
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>프록시에서 들어오는 연결 허용

프록시 설정에서 들어오는 연결을 허용합니다.

1, Windows 방화벽을 엽니다 **Windows Firewall with Advanced Security**합니다.
2. 마우스 오른쪽 단추로 **인바운드 규칙** > **새 규칙**을 클릭합니다.
3. **규칙 유형**에서 **사용자 지정** > **다음**을 선택합니다.
4. **프로그램**에서 **모든 프로그램** > **다음**을 선택합니다.
5. **프로토콜 및 포트**:
   - 유형을 설정 **TCP**
   - 설정할 **로컬 포트** 에 **특정 포트**
   - 설정할 **원격 포트** 하 **모든 포트**합니다.
  
6. 마법사를 끝내고 규칙 이름을 지정합니다.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>NSG에 프록시에 대한 예외 규칙 추가

NSG **NSF-lockdown**에서 10.0.0.5의 모든 포트에서 오는 트래픽을 포트 80(HTTP) 또는 443(HTTPS)의 모든 인터넷 주소에 허용합니다.

- 다음 PowerShell 스크립트에서는 트래픽을 허용하는 예제를 제공합니다.
- 모든 공용 인터넷 주소에 아웃바운드를 허용하는 대신 IP 주소 범위(-DestinationPortRange)를 지정하거나 storage.region 서비스 태그를 사용 할 수 있습니다.   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>FQDN 태그를 사용하여 방화벽 액세스 허용

Azure Backup에 대한 네트워크 트래픽의 아웃바운드 액세스를 허용하도록 Azure Firewall을 설정할 수 있습니다.

- Azure Firewall 배포에 대해 [알아보기](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)
- FQDN 태그에 대해 [읽어보기](https://docs.microsoft.com/azure/firewall/fqdn-tags)

## <a name="modify-storage-replication-settings"></a>저장소 복제 설정 수정

기본적으로 자격 증명 모음에는 [GRS(지역 중복 스토리지)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)가 있습니다.

- 기본 백업에 대 한 GRS이 좋습니다.
- 사용할 수 있습니다 [로컬 중복 저장소 (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 저렴 한 옵션에 대 한 합니다.

저장소 복제 유형을 다음과 같이 수정 합니다.

1. 포털에서 새 자격 증명 모음을 클릭 합니다. 아래는 **설정을** 섹션에서 클릭 **속성**합니다.
2. **속성**아래에 있는 **Backup 구성**, 클릭 **업데이트**합니다.
3. 저장소 복제 유형을 선택 하 고 클릭 **저장할**합니다.

      ![새 자격 증명 모음의 저장소 구성 설정](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>백업 정책 구성

구독에서 VM을 검색하여 백업을 구성합니다.

1. 자격 증명 모음 > **개요**에서 **+백업**을 클릭합니다.

   ![백업 단추](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **백업** 및 **백업 목표** 창이 열립니다.

2. **백업 목표**> **워크로드가 어디에서 실행됩니까?** 에서 **Azure**를 선택합니다. **무엇을 백업하고 싶습니까?** 에서 **가상 머신** >  **확인**을 선택합니다. 이렇게 하면 자격 증명 모음에 VM 확장을 등록합니다.

   ![백업 및 백업 목표 창](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   이 단계에서는 VM 확장을 자격 증명 모음에 등록합니다. **백업 목표** 창이 닫히고 **백업 정책** 창이 열립니다.

3. **백업 정책**에서 자격 증명 모음과 연결하려는 정책을 선택합니다. 그런 후 **OK**를 클릭합니다.
    - 드롭다운 메뉴 아래에 기본 정책의 상세 정보가 나열됩니다.
    - **새로 만들기**를 클릭하여 정책을 만듭니다. 정책을 정의하는 방법에 대해 [자세히 알아봅니다](backup-azure-arm-vms-prepare.md#configure-a-backup-policy).

      !["백업" 및 "백업 정책" 창](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. **가상 머신 선택** 창에서 지정된 된 백업 정책 > **확인**을 선택합니다.

   - 선택한 VM에 대해 유효성이 검사됩니다.
   - 자격 증명 모음과 동일한 지역에만 VM을 선택할 수 있습니다. VM은 단일 자격 증명 모음에만 백업할 수 있습니다.

     !["가상 머신 선택" 창](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. **백업**에서 **백업 사용**을 선택합니다.

   - 이렇게 하면 자격 증명 모음과 VM에 정책을 배포하고 Azure VM에서 실행되는 VM 에이전트에 백업 확장을 설치합니다.
   - 이 단계에서는 VM에 대한 초기 복구 지점을 만들지 않습니다.

     !["백업 사용" 단추](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

백업 사용 단추를 클릭한 후 다음을 수행합니다.

- 초기 백업은 백업 일정에 따라 실행됩니다.
- 백업 서비스는 VM의 실행 여부와 상관 없이 백업 확장을 설치합니다.
    - 실행 중인 VM은 애플리케이션 일치 복구 지점을 확보할 수 있는 큰 기회를 제공합니다.
    -  그러나 VM이 꺼져 있고 확장을 설치할 수 없는 경우에도 VM은 백업됩니다. 이 오프 라인 VM 이라고 합니다. 이 경우 복구 지점은 크래시 일치 됩니다.
    Azure Backup은 Azure VM 백업을 위한 일광 절약 시간 변경에 대한 자동 시계 조정을 지원하지 않습니다. 필요에 따라 수동으로 백업 정책을 수정합니다.

## <a name="run-the-initial-backup"></a>초기 백업 실행

초기 백업은 즉시 수동으로 실행하지 않으면 일정에 따라 실행됩니다. 다음과 같이 수동으로 실행합니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목**에서 **Azure Virtual Machine**을 클릭합니다.
3. **백업 항목** 목록에서 줄임표 **...** 를 클릭합니다.
4. **지금 백업**을 클릭합니다.
5. **지금 백업**에서 달력 컨트롤을 사용하여 복구 지점을 유지해야 하는 마지막 날을 선택하고 **확인**을 선택합니다.
6. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.



## <a name="next-steps"></a>다음 단계

- 문제를 해결 [Azure VM 에이전트](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 하거나 [Azure VM 백업](backup-azure-vms-troubleshoot.md)합니다.
- [복원](backup-azure-arm-restore-vms.md) Azure Vm입니다.

