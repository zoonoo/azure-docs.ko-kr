---
title: Azure 리소스 이동 기를 사용 하 여 Azure Vm을 다른 지역으로 이동 하기 위한 지원 매트릭스
description: Azure 리소스 이동 기를 사용 하 여 지역 간에 Azure Vm 이동에 대 한 지원을 검토 합니다.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: how-to
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: ddb1c68ab417390987ac4873a16b89757ec24789
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90058736"
---
# <a name="support-for-moving-azure-vms-between-azure-regions"></a>Azure 지역 간에 Azure Vm 이동에 대 한 지원

이 문서에는 리소스 이동 기를 사용 하 여 Azure 지역에서 가상 머신 및 관련 네트워크 리소스를 이동할 때 지원 및 필수 구성 요소가 요약 되어 있습니다.

> [!IMPORTANT]
> Azure 리소스 이동 기는 현재 미리 보기로 제공 됩니다.


## <a name="windows-vm-support"></a>Windows VM 지원

리소스 이동 기는 이러한 Windows 운영 체제를 실행 하는 Azure Vm을 지원 합니다.

**운영 체제** | **세부 정보**
--- | ---
Windows Server 2019 | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2016  | Server Core, 데스크톱 경험 포함 Server에 대해 지원됩니다.
Windows Server 2012 R2 | 지원됩니다.
Windows Server 2012 | 지원됩니다.
Windows Server 2008 R2 SP1/SP2 | 지원됩니다.<br/><br/> Windows Server 2008 R2 SP1/s p 2를 실행 하는 컴퓨터의 경우 Windows [서비스 스택 업데이트 (SSU)](https://support.microsoft.com/help/4490628) 및 [s h a-2 업데이트](https://support.microsoft.com/help/4474419)를 설치 해야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며, SHA-2 코드 서명이 사용 설정되지 않은 경우 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.
Windows 10(x64) | 지원됩니다.
Windows 8.1(x64) | 지원됩니다.
Windows 8(x64) | 지원됩니다.
Windows 7(x64) SP1 이상 | Windows 7 s p 1을 실행 하는 컴퓨터에 Windows [서비스 스택 업데이트 (SSU)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트](https://support.microsoft.com/help/4474419) 를 설치 합니다.  S h a-1은 9 월 2019에서 지원 되지 않으며, SHA-2 코드 서명을 사용 하도록 설정 하지 않으면 ' 준비 ' 단계가 실패 합니다. [SHA-2 업그레이드 및 요구 사항](https://aka.ms/SHA-2KB)에 대해 자세히 알아보세요.


## <a name="linux-vm-support"></a>Linux VM 지원

리소스 이동은 이러한 Linux 운영 체제를 실행 하는 Azure Vm을 지원 합니다.

**운영 체제** | **세부 정보**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions)
Ubuntu 16.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions)<br/><br/> 암호 기반 인증 및 로그인을 사용 하는 Ubuntu 서버와 클라우드 Vm을 구성 하는 클라우드 초기화 패키지는 클라우드 초기화 구성에 따라 장애 조치 (failover) 시 암호 기반 로그인을 사용 하지 않도록 설정할 수 있습니다. 암호 기반 로그인은 Azure Portal의 장애 조치 (failover) > 설정 메뉴 (장애 조치 (failover) > 문제 해결의 지원에서 암호를 다시 설정 하 여 가상 머신에서 했다가 다시 설정할 수 있습니다.
Ubuntu 18.04 LTS Server | [지원 되는 커널 버전](#supported-ubuntu-kernel-versions)입니다.
Debian 7 | [지원 되는 커널 버전](#supported-debian-kernel-versions)
Debian 8 | [지원 되는 커널 버전](#supported-debian-kernel-versions)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4 [지원되는 커널 버전](#supported-suse-linux-enterprise-server-12-kernel-versions)
SUSE Linux Enterprise Server 15 | 15 및 15 SP1 [(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-15-kernel-versions)
SUSE Linux Enterprise Server 11 | SP3
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> Red Hat 호환 커널 또는 UEK(Unbreakable Enterprise Kernel Release) 3, 4 및 5를 실행


### <a name="supported-ubuntu-kernel-versions"></a>지원 되는 Ubuntu 커널 버전

**릴리스** | **커널 버전** 
--- | --- 
14.04 LTS |  3.13.0-24-generic에서 3.13.0-170-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-148-generic<br/>4.15.0-1023-azure에서 4.15.0-1045-azure 
16.04 LTS |  4.4.0-21-generic에서 4.4.0-171-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-74-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1066-azure
18.04 LTS | 4.15.0-20-generic에서 4.15.0-74-generic </br> 4.18.0-13-generic에서 4.18.0-25-generic </br> 5.0.0-15-generic에서 5.0.0-37-generic </br> 5.3.0-19-generic에서 5.3.0-24-generic </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1028-azure </br> 5.3.0-1007-azure에서 5.3.0-1009-azure


### <a name="supported-debian-kernel-versions"></a>지원 되는 Debian 커널 버전 

**릴리스** |  **커널 버전** 
--- |  --- 
Debian 7 |  3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 
Debian 8 |  3.16.0-4-amd64에서 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.11-amd64 
Debian 8 |  3.16.0-4-amd64에서 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.9-amd64

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions"></a>지원 되는 SUSE Linux Enterprise Server 12 커널 버전 

**릴리스** | **커널 버전** 
--- |  --- 
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) |  모든 [STOCK SUSE 12 SP1, SP2, SP3, SP4 커널을](https://www.suse.com/support/kb/doc/?id=000019587) 지원 합니다.</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br>4.12.14-6.3-azure에서 4.12.14-6.34-azure  


### <a name="supported-suse-linux-enterprise-server-15-kernel-versions"></a>지원 되는 SUSE Linux Enterprise Server 15 커널 버전

**릴리스** | **커널 버전** |
--- |  --- |
SUSE Linux Enterprise Server 15 및 15 SP1 |  모든 stock SUSE 15 및 15 커널을 지원 합니다.</br></br> 4.12.14-5.5-azure에서 4.12.14-8.22-azure |

## <a name="supported-linux-file-systemguest-storage"></a>지원 되는 Linux 파일 시스템/게스트 저장소

* 파일 시스템: ext3, ext4, XFS, BTRFS
* 볼륨 관리자: LVM2
* 다중 경로 소프트웨어: 디바이스 매퍼


## <a name="supported-vm-compute-settings"></a>지원 되는 VM 계산 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
크기 | CPU 코어가 2 개 이상이 고 1GB RAM이 있는 모든 Azure VM 크기 | [Azure Virtual Machine 크기](../virtual-machines/sizes-general.md)를 확인합니다.
가용성 집합 | 현재 지원되지 않음 | 가용성 집합이 있는 Azure VM을 기본 옵션을 사용 하 여 이동 컬렉션에 추가 하면 준비 프로세스가 실패 합니다. VM을 가용성 영역으로 이동 하거나 단일 인스턴스 VM으로 이동 하도록 선택할 수 있습니다. 대상 속성 편집 페이지에서 이러한 설정을 수정할 수 있습니다.
가용성 영역 | 지원됨 | 대상 지역 지원에 따라 지원 됩니다.
Azure 갤러리 이미지 (Microsoft에서 게시) | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Azure 갤러리 이미지 (타사에서 게시)  | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
사용자 지정 이미지 (타사에서 게시)| 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Site Recovery를 사용 하는 Vm | 지원되지 않음 | 백 엔드에서 Site Recovery를 사용 하 여 Vm의 지역 간에 리소스를 이동 합니다. 이미 Site Recovery를 사용 하 고 있는 경우 복제를 사용 하지 않도록 설정 하 고 준비 프로세스를 시작 합니다.
RBAC 정책 | 지원되지 않음 | Vm의 RBAC (역할 기반 액세스 제어) 정책이 대상 지역의 VM에 복사 되지 않습니다.
확장 | 지원되지 않음 | 확장은 대상 지역의 VM에 복사 되지 않습니다. 이동이 완료 된 후 수동으로 설치 합니다.


## <a name="supported-vm-storage-settings"></a>지원 되는 VM 저장소 설정

이 표에서는 Azure VM OS 디스크, 데이터 디스크 및 임시 디스크에 대한 지원을 요약해서 표시합니다. 성능 문제를 방지하려면 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 및 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM의 VM 디스크 제한 및 목표를 확인하는 것이 중요합니다.

**구성 요소** | **지원** | **세부 정보**
--- | --- | ---
OS 디스크 최대 크기 | 2048GB | VM 디스크에 대해 [자세히 알아봅니다](../virtual-machines/windows/managed-disks-overview.md).
임시 디스크 | 지원되지 않음 | 임시 디스크는 항상 준비 프로세스에서 제외 됩니다.<br/><br/> 임시 디스크에는 영구 데이터를 저장하지 마세요. [자세히 알아보기](../virtual-machines/windows/managed-disks-overview.md#temporary-disk).
데이터 디스크 최대 크기 | 관리 디스크의 경우 8,192GB
데이터 디스크 최소 크기 |  관리 디스크의 경우 2GB |
데이터 디스크 최대 수 | 특정 Azure VM 크기에 대한 지원에 따라 최대 64개 | VM 크기에 대해 [자세히 알아봅니다](../virtual-machines/windows/sizes.md).
데이터 디스크 변경 비율 | Premium Storage는 디스크당 최대 10MBps입니다. Standard Storage는 디스크당 최대 2MBps입니다. | 디스크의 평균 데이터 변경률이 지속적으로 최대값 보다 높으면 준비가 되지 않습니다.<br/><br/>  그러나 최대값이 산발적으로 초과 되 면 준비를 수행할 수 있지만 약간 지연 된 복구 지점이 표시 될 수도 있습니다.
데이터 디스크 (표준 저장소 계정) | 지원되지 않습니다. | 저장소 유형을 managed disk로 변경한 다음, VM을 이동 해 보세요.
데이터 디스크 (Premium storage 계정) | 지원되지 않음 | 저장소 유형을 managed disk로 변경한 다음, VM을 이동 해 보세요.
관리 디스크 (표준) | 지원됨  |
관리 디스크 (프리미엄) | 지원됨 |
표준 SSD | 지원됨 |
2세대(UEFI 부팅) | 지원됨
부트 진단 저장소 계정 | 지원되지 않음 | VM을 대상 지역으로 이동한 후 다시 활성화 합니다.

### <a name="limits-and-data-change-rates"></a>제한 및 데이터 변경률

다음 표에서는 테스트에 따른 제한을 요약 하 여 보여 줍니다. 이러한 모든 가능한 응용 프로그램 i/o 조합은 다루지 않습니다. 실제 결과는 응용 프로그램 i/o 조합에 따라 달라 집니다. 디스크 데이터 변동 별 및 VM 데이터 변동 당 두 가지를 고려해 야 합니다.

**스토리지 대상** | **평균 원본 디스크 I/O** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20MB/s | 디스크당 1,684GB

## <a name="supported-vm-networking-settings"></a>지원 되는 VM 네트워킹 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
NIC | 지원됨 | 대상 지역에 기존 리소스를 지정 하거나 준비 프로세스 중에 새 리소스를 만듭니다. 
내부 부하 분산 장치 | 지원됨 | 대상 지역에 기존 리소스를 지정 하거나 준비 프로세스 중에 새 리소스를 만듭니다.  
공용 부하 분산 장치 | 현재 지원되지 않음 | 대상 지역에 기존 리소스를 지정 하거나 준비 프로세스 중에 새 리소스를 만듭니다.  
공용 IP 주소 | 지원됨 | 대상 지역에 기존 리소스를 지정 하거나 준비 프로세스 중에 새 리소스를 만듭니다.  
네트워크 보안 그룹 | 지원됨 | 대상 지역에 기존 리소스를 지정 하거나 준비 프로세스 중에 새 리소스를 만듭니다.  
예약된(고정) IP 주소 | 지원됨 | 현재이를 구성할 수 없습니다. 값은 기본적으로 원본 값이 됩니다. <br/><br/> 원본 VM의 NIC에 고정 IP 주소가 있고 대상 서브넷에 동일한 IP 주소가 사용 가능한 경우 대상 VM에 할당 됩니다.<br/><br/> 대상 서브넷에 사용할 수 있는 IP 주소가 동일 하지 않은 경우 VM에 대 한 이동 시작이 실패 합니다.
동적 IP 주소 | 지원됨 | 현재이를 구성할 수 없습니다. 값은 기본적으로 원본 값이 됩니다.<br/><br/> 원본 NIC에 동적 IP 주소가 지정 된 경우 대상 VM의 NIC도 기본적으로 동적입니다.
IP 구성 | 지원됨 | 현재이를 구성할 수 없습니다. 값은 기본적으로 원본 값이 됩니다.

## <a name="outbound-access-requirements"></a>아웃 바운드 액세스 요구 사항

이동 하려는 Azure Vm에는 아웃 바운드 액세스가 필요 합니다.


### <a name="url-access"></a>URL 액세스

 URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 다음 URL에 대한 액세스를 허용합니다.

**이름** | **Azure 퍼블릭 클라우드** | **세부 정보** 
--- | --- | --- 
스토리지 | `*.blob.core.windows.net`  | VM에서 원본 지역의 캐시 스토리지 계정에 데이터를 쓸 수 있도록 합니다. 
Azure Active Directory | `login.microsoftonline.com`  | Site Recovery 서비스 URL에 대한 권한 부여 및 인증을 제공합니다. 
복제 | `*.hypervrecoverymanager.windowsazure.com` | VM이 Site Recovery 서비스와 통신할 수 있도록 합니다. 
Service Bus | `*.servicebus.windows.net` | VM이 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 합니다. 

## <a name="nsg-rules"></a>NSG 규칙
NSG (네트워크 보안 그룹) 규칙을 사용 하 여 아웃 바운드 연결을 제어 하는 경우 이러한 [서비스 태그](../virtual-network/service-tags-overview.md) 규칙을 만듭니다. 각 규칙은 HTTPS (443)에 대 한 아웃 바운드 액세스를 허용 해야 합니다.
- 원본 영역에 대 한 저장소 태그 규칙을 만듭니다.
- 모든 지역의 Site Recovery 서비스에 대 한 액세스를 허용 하는 *AzureSiteRecovery* 태그 규칙을 만듭니다. 이 태그는 이러한 다른 태그에 종속 되므로 다음에 대 한 규칙을 만들어야 합니다.
    - *AzureActiveDirectory*
    - **EventHub*
    - *AzureKeyVault*
    - *GuestAndHybridManagement*
- 비프로덕션 환경에서 규칙을 테스트 하는 것이 좋습니다. [몇 가지 예를 검토](../site-recovery/azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)합니다. 


## <a name="next-steps"></a>다음 단계

리소스 이동 기를 사용 하 여 [AZURE VM](tutorial-move-region-virtual-machines.md) 을 다른 지역으로 이동 해 보세요.