---
title: Azure Backup을 사용하여 Recovery Services 자격 증명 모음의 Azure VM 백업
description: Azure Backup을 사용하여 Recovery Services 자격 증명 모음의 Azure VM을 백업하는 방법을 설명합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: e782afb971f95a654119d9817edeef02642bee9e
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447568"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 Azure VM 백업

이 문서에서는 Recovery Services 자격 증명 모음에 백업을 배포하고 사용하도록 설정하여 [Azure Backup](backup-overview.md)에서 Azure VM을 백업하는 방법을 설명합니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 지원되는 시나리오 및 필수 구성 요소를 확인합니다.
> * Azure VM을 준비합니다. 필요한 경우 Azure VM 에이전트를 설치하고 VM에 대한 아웃바운드 액세스를 확인합니다.
> * 자격 증명 모음을 만듭니다.
> * 자격 증명 모음에 대해 스토리지 설정
> * VM을 검색하고 백업 설정 및 정책을 구성합니다.
> * Azure VM에 백업 사용


> [!NOTE]
   > 이 문서에서는 자격 증명 모음을 설정하고 백업할 VM을 선택하여 Azure VM을 백업하는 방법을 설명합니다. 여러 VM을 백업하는 경우에 유용합니다. 또한 해당 VM 설정에서 직접 [Azure VM을 백업](backup-azure-vms-first-look-arm.md)할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다.

1. Azure VM 백업 아키텍처를 [검토](backup-architecture.md#architecture-direct-backup-of-azure-vms)합니다.
Azure VM 백업 및 백업 확장에 대해 [자세히 알아봅니다](backup-azure-vms-introduction.md).
2. Azure VM 백업에 대한 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.
3. Azure VM을 준비합니다. VM 에이전트가 설치되지 않은 경우 설치하고, 백업하려는 VM에 대한 아웃바운드 액세스를 확인합니다.


## <a name="prepare-azure-vms"></a>Azure VM 준비

필요한 경우 VM 에이전트를 설치하고 VM에 대한 아웃바운드 액세스를 확인합니다.

### <a name="install-the-vm-agent"></a>VM 에이전트 설치 
필요한 경우 다음과 같이 에이전트를 설치합니다.

**VM** | **세부 정보**
--- | ---
**Windows VM** | 에이전트 MSI 파일을 [다운로드하여 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다. 컴퓨터의 관리자 권한으로 설치합니다.<br/><br/> 설치를 확인하려면 VM의 *C:\WindowsAzure\Packages*에서 마우스 오른쪽 단추로 WaAppAgent.exe > **속성** > **세부 정보** 탭을 클릭합니다. **제품 버전**은 2.6.1198.718 이상이어야 합니다.<br/><br/> 에이전트를 업데이트하는 경우 백업 작업이 실행되고 있지 않은지 확인하고 [에이전트를 다시 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)합니다.
**Linux VM** | 배포 패키지에서 리포지토리의 RPM 또는 DEB 패키지를 사용한 설치는 선호하는 Azure Linux Azure 설치 및 업그레이드 방법입니다. 모든 [인증 배포 공급자](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)는 이미지 및 리포지토리에 Azure Linux 에이전트 패키지를 통합합니다. 에이전트는 [GitHub](https://github.com/Azure/WALinuxAgent)에서 사용할 수 있지만 설치하지 않는 것이 좋습니다.<br/><br/> 에이전트를 업데이트하는 경우 백업 작업이 실행되고 있지 않은지 확인하고 이진 파일을 업데이트합니다. 


### <a name="establish-network-connectivity"></a>네트워크 연결 설정

VM에서 실행 중인 백업 확장은 Azure 공용 IP 주소에 아웃바운드 액세스 권한이 있어야 합니다.

- Azure VM에서 Azure Backup 서비스와 통신하기 위해 명시적 아웃바운드 네트워크 액세스 권한이 필요하지 않습니다.
- 하지만 연결을 시도할 때 특정 이전 가상 머신에서 문제가 발생하며 **ExtensionSnapshotFailedNoNetwork** 오류로 실패할 수 있습니다. 이 경우 백업 확장이 백업 트래픽의 Azure 공용 IP 주소를 전달할 수 있도록 다음 옵션 중 하나를 사용합니다.

   **옵션** | **작업** ** | **장점** | **단점**
   --- | --- | --- | ---
   **NSG 규칙 설정** | [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 허용합니다.<br/><br/>  개별적으로 모든 주소 범위를 허용하고 관리하는 대신 [서비스 태그](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)를 사용하여 Azure Backup 서비스에 액세스를 허용하는 규칙을 추가할 수 있습니다. [서비스 태그](../virtual-network/security-overview.md#service-tags)에 대해 자세히 알아보세요. | 추가 비용 없음 서비스 태그를 사용한 간편한 관리
   **프록시 배포** | 트래픽 라우팅을 위해 HTTP 프록시 서버를 배포합니다. | 저장소뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다. 스토리지 URL에 대한 세분화된 제어가 허용됩니다.<br/><br/> VM에 대한 인터넷 액세스의 단일 지점입니다.<br/><br/> 프록시에 대한 추가 비용이 없습니다.<br/><br/> 
   **Azure Firewall 설정** | Azure Backup 서비스에 대한 FQDN 태그를 사용하여 VM에서 Azure Firewall을 통해 트래픽을 허용합니다.|  VNet 서브넷에서 Azure Firewall 설정이 있는 경우 간편하게 사용합니다. | 태그에 사용자 고유의 FQDN을 만들거나 FQDN 태그를 수정할 수 없습니다.<br/><br/> Azure Managed Disks를 사용하는 경우 방화벽에서 열려 있는 추가 포트(포트 8443)가 필요할 수 있습니다.

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Azure에 아웃바운드 액세스를 허용하도록 NSG 규칙 설정

Azure VM에 NSG가 관리하는 액세스 권한이 있는 경우 필요한 범위 및 포트에 백업 스토리지에 대한 아웃바운드 액세스를 허용합니다.

1. VM > **네트워킹**에서 **아웃바운드 포트 규칙 추가**를 클릭합니다.
2. **아웃바운드 보안 규칙 추가**에서 **고급**을 클릭합니다.
3. **원본**에서 **VirtualNetwork**를 선택합니다.
4. **원본 포트 범위**에서 별표(*)를 입력하여 모든 포트에 아웃바운드 액세스를 허용합니다.
5. **대상**에서 **서비스 태그**를 선택합니다. 목록에서 **Storage.region**을 선택합니다. 지역은 자격 증명 모음이 있고 백업하려는 VM이 위치하는 지역입니다.
6. **대상 포트 범위**에서 포트를 선택합니다.
    - 암호화되지 않은 스토리지 계정을 사용하는 비관리형 VM: 80
    - 암호화된 스토리지 계정을 사용하는 비관리형 VM: 443(기본 설정)
    - 관리형 VM: 8443
7. **프로토콜**에서 **TCP**를 선택합니다.
8. **우선 순위**에서 모든 거부 규칙보다 더 작은 우선 순위 값을 지정합니다. 액세스를 거부하는 규칙이 있는 경우 새 허용 규칙은 더 높아야 합니다. 예를 들어 우선 순위 1000에서 **Deny_All** 규칙이 있는 경우 새 규칙은 1000 미만으로 설정해야 합니다.
9. 규칙에 대한 이름과 설명을 입력하고 **OK**를 클릭합니다.

아웃바운드 액세스를 허용하는 여러 VM에 NSG 규칙을 적용할 수 있습니다.

이 비디오에서는 프로세스에 대해 설명합니다.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>프록시를 통해 백업 트래픽 라우팅

프록시를 통해 백업 트래픽을 라우팅한 다음, 필요한 Azure 범위에 액세스할 수 있는 권한을 프록시에 부여할 수 있습니다.
다음과 같이 허용하도록 VM을 구성해야 합니다.

- Azure VM은 프록시를 통해 공용 인터넷으로 향하는 모든 HTTP 트래픽을 라우팅해야 합니다.
- 프록시는 해당 가상 네트워크(VNet)의 VM에서 들어오는 트래픽을 허용해야 합니다.
- NSG **NSF-lockdown**은 프록시 VM에서 들어오는 아웃바운드 인터넷 트래픽을 허용하는 규칙이 필요합니다.

##### <a name="set-up-the-proxy"></a>프록시 설정
시스템 계정 프록시가 없는 경우 다음과 같이 설정합니다.

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553)을 다운로드합니다.

2. **PsExec.exe-i-s cmd.exe**를 실행하여 시스템 계정에서 명령 프롬프트를 실행합니다.
3. 시스템 컨텍스트에서 브라우저를 실행합니다. 예: Internet Explorer의 경우 **%PROGRAMFILES%\Internet Explorer\iexplore.exe**  
4. 프록시 설정을 정의합니다.
    - Linux 머신:
        - 이 줄을 **/etc/environment** 파일에 추가합니다.
            - **http_proxy=http://proxy IP address:proxy port**
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

##### <a name="allow-incoming-connections-on-the-proxy"></a>프록시에서 들어오는 연결 허용

프록시 설정에서 들어오는 연결을 허용합니다.

- 예를 들어 **고급 보안을 사용하는 Windows Firewall**을 엽니다.
    - 마우스 오른쪽 단추로 **인바운드 규칙** > **새 규칙**을 클릭합니다.
    - **규칙 유형**에서 **사용자 지정** > **다음**을 선택합니다.
    - **프로그램**에서 **모든 프로그램** > **다음**을 선택합니다.
    - **프로토콜 및 포트**에서 유형을 **TCP**로, **로컬 포트**를 **특정 포트**로, **원격 포트**를 **모든 포트**로 지정합니다.
    - 마법사를 끝내고 규칙 이름을 지정합니다.

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>NSG에 프록시에 대한 예외 규칙 추가

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

## <a name="create-a-vault"></a>자격 증명 모음 만들기

자격 증명 모음은 백업과 시간 경과에 따라 생성된 복구 지점을 저장하고 백업된 머신과 연결된 백업 정책을 저장합니다. 다음과 같이 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **허브** 메뉴에서 **찾아보기**를 선택한 다음, **Recovery Services**를 입력합니다. **Recovery Services 자격 증명 모음**을 선택합니다.

    ![상자에 입력 및 결과에서 "Recovery Services 자격 증명 모음" 선택](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    !["Recovery Services 자격 증명 모음" 창](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. **Recovery Services 자격 증명 모음** >  **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다.
    - 이름은 Azure 구독에 대해 고유해야 합니다.
    - 2~50자를 포함할 수 있습니다.
    - 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.
5. **구독**을 선택하여 사용 가능한 구독 목록을 볼 수 있습니다. 사용할 구독을 잘 모르는 경우 기본(또는 제안된) 구독을 사용합니다. 회사 또는 학교 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
6. **리소스 그룹**을 선택하여 사용 가능한 리소스 그룹 목록을 표시하거나 **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다. 리소스 그룹에 대해 [자세히 알아봅니다](../azure-resource-manager/resource-group-overview.md).
7. **위치**를 선택하여 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음은 *반드시* 백업하려는 VM과 동일한 지역에 있어야 합니다.
8. **만들기**를 선택합니다.
    - 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다.
    - 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다.
    ![백업 자격 증명 모음 목록](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에 표시됩니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

## <a name="set-up-storage-replication"></a>스토리지 복제 설정

기본적으로 자격 증명 모음에는 [GRS(지역 중복 스토리지)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)가 있습니다. 기본 백업용으로 GRS를 권장하지만 저렴한 옵션으로 [로컬 중복 스토리지](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 사용할 수 있습니다.

다음과 같이 스토리지 복제를 수정합니다.

1. 자격 증명 모음 > **백업 인프라**에서 **백업 구성**을 클릭합니다.

   ![백업 자격 증명 모음 목록](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. **백업 구성**에서 필요에 따라 스토리지 중복 메서드를 수정한 다음, **저장**을 선택합니다.


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
    -  그러나 VM이 꺼져 있고 확장을 설치할 수 없는 경우에도 VM은 백업됩니다. *오프라인 VM*이라고 합니다. 이 경우 복구 지점은 *충돌 일치*가 됩니다.
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

- [Azure VM 에이전트](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 또는 [Azure VM 백업](backup-azure-vms-troubleshoot.md)에서 발생하는 문제를 해결합니다.
- [Azure VM 백업](backup-azure-vms-first-look-arm.md)
