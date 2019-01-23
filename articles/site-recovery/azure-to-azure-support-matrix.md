---
title: Azure Site Recovery를 사용한 Azure 지역 간 Azure IaaS VM 재해 복구에 대한 Azure Site Recovery 지원 매트릭스 | Microsoft Docs
description: 재해 복구(DR) 요구 사항에 따라 지역 간에 Azure VM(가상 머신)을 Azure Site Recovery 복제하기 위한 구성 및 지원되는 운영 체제를 요약합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/03/2019
ms.author: raynew
ms.openlocfilehash: 7e5f72ca637cb657369a3b384aee666e0935b9d0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263549"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>한 Azure 지역에서 다른 지역으로 복제를 위한 지원 매트릭스

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure 지역 간에 Azure VM의 복제, 장애 조치(Failover) 및 복구를 포함하는 재해 복구를 배포할 때 지원되는 구성 및 구성 요소를 요약하여 설명합니다.


## <a name="deployment-method-support"></a>배포 방법 지원 여부

**배포 방법** |  **지원됨/지원되지 않음**
--- | ---
**Azure Portal** | 지원됨
**PowerShell** | [PowerShell을 사용한 Azure 간 복제](azure-to-azure-powershell.md)
**REST API** | 현재 지원되지 않음
**CLI** | 현재 지원되지 않음


## <a name="resource-support"></a>리소스 지원

**리소스 작업** | **세부 정보**
--- | --- | ---
**리소스 그룹 간 자격 증명 모음 이동** | 지원되지 않음
**리소스 그룹 간에 계산/저장소/네트워크 리소스 이동** | 지원되지 않습니다.<br/><br/> VM 복제 후 VM 또는 저장소/네트워크와 같은 관련 구성 요소를 이동하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
**재해 복구를 위해 한 구독에서 다른 구독으로 Azure VM 복제** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**지원되는 지역별 클러스터 내의 여러 지역 간에 VM 마이그레이션(구독 내/구독 간)** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**동일한 지역 내에서 VM 마이그레이션** | 지원되지 않습니다.

## <a name="region-support"></a>지역 지원

동일한 지리적 클러스터 내의 두 지역 간에 VM을 복제 및 복구할 수 있습니다. 지리적 클러스터는 데이터 대기 시간 및 주권에 유의해서 정의해야 합니다.


**지리적 클러스터** | **Azure 지역**
-- | --
아메리카 | 캐나다 동부, 캐나다 중부, 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중부, 미국 중북부
유럽 | 영국 서부, 영국 남부, 북유럽, 유럽 서부, 프랑스 중부, 프랑스 남부
아시아 | 인도 남부, 인도 중부, 동남 아시아, 동아시아, 일본 동부, 일본 서부, 한국 중부, 한국 남부
오스트레일리아   | 오스트레일리아 동부, 오스트레일리아 남동부, 오스트레일리아 중부, 오스트레일리아 중부 2
Azure Government    | 미국 버지니아 주 정부, 미국 아이오와 주 정부, 미국 애리조나 주 정부, 미국 텍사스 주 정부, US DoD 동부, US DoD 중부
독일 | 독일 중부, 독일 북동부
중국 | 중국 동부, 중국 북부, 중국 북부 2, 중국 동부 2

>[!NOTE]
>
> - **브라질 남부** 지역의 경우 복제한 후 다음 중 하나로 장애 조치(failover)할 수 있습니다. 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중북부 지역 Site Recovery는 브라질 남부만 VM을 보호할 수 있는 원본 지역으로 사용할 수 있도록 설정했습니다. 미국 중남부와 같은 Azure 지역의 경우 **대상 DR 지역으로 사용할 수 없습니다**. 지리적 거리로 인한 대기 시간이 확인되므로 브라질 남부 이외의 아메리카 지역을 선택하는 것이 좋습니다.
> 
> - **자격 증명 모음을 만들려면** **지역을 볼 수 없으면** 구독이 해당 지역에 리소스를 만들 수 있는 권한이 있는지 확인합니다. 예:  프랑스 남부에서 자격 증명 모음을 만들 수 없는 경우 구독이 프랑스 남부 지역에 액세스할 수 없는 것입니다. 문제 유형 "구독 관리" 및 문제 유형 "기타 일반 질문"의 지원 티켓을 "XXX Azure 지역의 구독을 허용 목록에 추가" 제목으로 정리합니다.
> 
> - **복제를 사용하도록 설정하는 동안** 지리적 클러스터 내의 **지역을 볼 수 없는 경우** 구독이 해당 지역에서 가상 머신을 만들 수 있는 액세스 권한이 있는지 확인합니다. 예:  프랑스 중부에서 프랑스 남부로의 가상 머신을 보호하려고 하며 지역 드롭다운 아래에 프랑스 남부가 표시되지 않으면 구독에 VM을 해당 지역에 배포하기 위한 액세스 권한이 없는 것입니다. 문제 유형 "구독 관리" 및 문제 유형 "기타 일반 질문"의 지원 티켓을 "XXX Azure 지역의 구독을 허용 목록에 추가" 제목으로 정리합니다.
> - 위에서 언급한 지리적 클러스터 간의 지역은 선택할 수 없습니다.


## <a name="cache-storage"></a>캐시 저장소

이 표에서는 복제하는 동안 Site Recovery에서 사용하는 캐시 저장소 계정에 대한 지원을 요약해서 보여 줍니다.

**설정** | **지원** | **세부 정보**
--- | --- | ---
범용 V2 저장소 계정(핫 및 쿨 계층) | 지원되지 않습니다. | V2의 트랜잭션 비용이 V1 저장소 계정 비용보다 훨씬 더 높기 때문에 캐시 저장소에 대한 제한이 있습니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 방화벽 지원 캐시 스토리지 계정 또는 대상 스토리지 계정을 사용하는 경우 ['신뢰할 수 있는 Microsoft 서비스 허용'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)을 선택해야 합니다.


## <a name="replicated-machine-operating-systems"></a>복제된 컴퓨터 운영 체제

Site Recovery는 이 섹션에 나열된 운영 체제를 실행하는 Azure VM의 복제를 지원합니다.

### <a name="windows"></a> Windows

**운영 체제** | **세부 정보**
--- | ---
Windows Server 2016  | Server Core, 데스크톱 경험이 있는 Server
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | SP1 이상 실행

#### <a name="linux"></a>Linux

**운영 체제** | **세부 정보**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> 암호 기반 인증 및 로그인을 사용하고, cloud-init 패키지를 사용하여 클라우드 VM을 구성하는 Ubuntu 서버의 암호 기반 로그인은 장애 조치(failover) 시 사용되지 않도록 설정할 수 있습니다(cloudinit 구성에 따라 다름). Azure Portal에서 장애 조치(failover)된 VM의 지원 > 문제 해결 > 설정 메뉴에서 암호를 재설정하여 암호 기반 로그인을 다시 사용하도록 설정할 수 있습니다.
Debian 7 | [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1,SP2,SP3. [(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 복제 컴퓨터를 SP3에서 SP4로 업그레이드하는 것은 지원되지 않습니다. 복제된 컴퓨터를 업그레이드한 경우 복제를 사용하지 않도록 설정하고 업그레이드 후에 다시 사용하도록 설정해야 합니다.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Red Hat 호환 커널 또는 UEK3(Unbreakable Enterprise Kernel Release 3) 실행


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Ubuntu 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | 9.21 | 3.13.0-24-generic에서 3.13.0-163-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-140-generic<br/>4.15.0-1023-azure에서 4.15.0-1035-azure |
14.04 LTS | 9.20 | 3.13.0-24-generic에서 3.13.0-161-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-138-generic<br/>4.15.0-1023-azure에서 4.15.0-1030-azure |
14.04 LTS | 9.19 | 3.13.0-24-generic에서 3.13.0-153-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-generic에서 3.13.0-151-generic<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-generic에서 4.4.0-128-generic |
|||
16.04 LTS | 9.21 | 4.4.0-21-generic에서 4.4.0-140-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-42-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1035-azure|
16.04 LTS | 9.20 | 4.4.0-21-generic에서 4.4.0-138-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-38-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1030-azure|
16.04 LTS | 9.19 | 4.4.0-21-generic에서 4.4.0-131-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13-generic에서 4.15.0-30-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-generic에서 4.4.0-128-generic<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure |


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Debian 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | 9.18,9.19,9.20,9.21 | 3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.20, 9.21 | 3.16.0-4-amd64에서 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.19 | 3.16.0-4-amd64에서 3.16.0-6-amd64, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.18 | 3.16.0-4-amd64에서 3.16.0-6-amd64까지, 4.9.0-0.bpo.4-amd64에서 4.9.0-0.bpo.6-amd64까지 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 가상 머신에 대해 지원되는 SUSE Linux Enterprise Server 12 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3) | 9.21 | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default에서 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default에서 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default에서 4.4.162-94.72-default |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3) | 9.20 | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default에서 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default에서 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default에서 4.4.162-94.69-default |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3) | 9.19 | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default에서 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default에서 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default에서 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12(SP1,SP2,SP3) | 9.18 | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default에서 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default에서 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default에서 4.4.138-94.39-default |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>복제된 컴퓨터 - Linux 파일 시스템/게스트 저장소

* 파일 시스템: ext3, ext4, ReiserFS(Suse Linux Enterprise Server만), XFS, BTRFS
* 볼륨 관리자: LVM2
* 다중 경로 소프트웨어: 디바이스 매퍼


## <a name="replicated-machines---compute-settings"></a>복제된 컴퓨터 - 계산 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
크기 | CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM | [Azure Virtual Machine 크기](../virtual-machines/windows/sizes.md)를 확인합니다.
가용성 집합 | 지원됨 | 기본 옵션을 사용하여 Azure VM에 대해 복제를 사용하도록 설정하면 원본 지역 설정에 따라 가용성 집합이 자동으로 생성됩니다. 이러한 설정을 수정할 수 있습니다.
가용성 영역 | 지원됨 |
HUB(하이브리드 사용 혜택) | 지원됨 | 원본 VM에 활성 HUB 라이선스가 있는 경우 테스트 장애 조치(failover) 또는 장애 조치(failover)된 VM에서도 HUB 라이선스를 사용합니다.
VM 확장 집합 | 지원되지 않음 |
Azure 갤러리 이미지 - Microsoft 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Azure 갤러리 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
사용자 지정 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Site Recovery를 사용하여 마이그레이션된 VM | 지원됨 | VMware VM 또는 물리적 컴퓨터가 Site Recovery를 사용하여 Azure에 마이그레이션되면 컴퓨터에서 실행되는 이전 버전의 모바일 서비스를 제거하고 컴퓨터를 다시 시작한 후 다른 Azure 지역에 복제해야 합니다.

## <a name="replicated-machines---disk-actions"></a>복제된 컴퓨터 - 디스크 작업

**작업** | **세부 정보**
-- | ---
복제된 VM에서 디스크 크기 조정 | 지원됨
복제된 VM에 디스크 추가 | 지원되지 않습니다.<br/><br/> VM에 복제를 사용하지 않도록 설정하고, 디스크를 추가한 다음, 복제를 다시 사용하도록 설정해야 합니다.

## <a name="replicated-machines---storage"></a>복제된 컴퓨터 - 저장소

이 표에서는 Azure VM OS 디스크, 데이터 디스크 및 임시 디스크에 대한 지원을 요약해서 표시합니다.

- 성능 문제를 방지하려면 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 및 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM의 VM 디스크 제한 및 목표를 확인하는 것이 중요합니다.
- 기본 설정을 사용하여 배포하는 경우 Site Recovery는 원본 설정에 따라 디스크 및 저장소 계정을 자동으로 만듭니다.
- 사용자 지정하는 경우 지침을 따릅니다.

**구성 요소** | **지원** | **세부 정보**
--- | --- | ---
OS 디스크 최대 크기 | 2048GB | VM 디스크에 대해 [자세히 알아봅니다](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms).
임시 디스크 | 지원되지 않음 | 임시 디스크는 항상 복제에서 제외됩니다.<br/><br/> 임시 디스크에는 영구 데이터를 저장하지 않도록 합니다. [자세히 알아보기](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
데이터 디스크 최대 크기 | 4,095GB |
데이터 디스크 최대 수 | 특정 Azure VM 크기에 대한 지원에 따라 최대 64개 | VM 크기에 대해 [자세히 알아봅니다](../virtual-machines/windows/sizes.md).
데이터 디스크 변경 비율 | Premium Storage는 디스크당 최대 10MBps입니다. Standard Storage는 디스크당 최대 2MBps입니다. | 디스크의 평균 데이터 변경률이 계속해서 최대값보다 큰 경우 복제가 처리되지 않습니다.<br/><br/>  그러나 최대값을 산발적으로 초과하는 경우 복제가 처리될 수 있지만 복구 지점이 약간 지연될 수 있습니다.
데이터 디스크 - Standard Storage 계정 | 지원됨 |
데이터 디스크 - Premium Storage 계정 | 지원됨 | VM의 디스크가 Premium Storage 계정과 표준 스토리지 계정에 분산된 경우 대상 지역의 스토리지를 동일하게 구성하기 위해 각 디스크에 대해 서로 다른 대상 스토리지 계정을 선택할 수 있습니다.
Managed Disk - Standard | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
Managed Disk - Premium | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
표준 SSD | 지원되지 않음 |
중복 | LRS 및 GRS가 지원됩니다.<br/><br/> ZRS는 지원되지 않습니다.
콜드 및 핫 저장소 | 지원되지 않음 | VM 디스크는 콜드 및 핫 저장소에서 지원되지 않습니다.
저장소 공간 | 지원됨 |
미사용 암호화(SSE) | 지원됨 | SSE은 저장소 계정의 기본 설정입니다.   
Windows OS용 ADE(Azure Disk Encryption) | [Azure AD 앱을 사용한 암호화](https://aka.ms/ade-aad-app)를 사용할 수 있도록 설정된 VM은 지원됩니다. |
Linux OS용 ADE(Azure Disk Encryption) | 지원되지 않음 |
디스크 핫 추가/제거 | 지원되지 않음 | VM에서 데이터 디스크를 추가하거나 제거하는 경우 해당 VM에 대해 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
디스크 제외 | 지원되지 않음|   임시 디스크는 기본적으로 제외됩니다.
직접 저장소 공간  | 지원되지 않음|
스케일 아웃 파일 서버  | 지원되지 않음|
LRS | 지원됨 |
GRS | 지원됨 |
RA-GRS | 지원됨 |
ZRS | 지원되지 않음 |
콜드 및 핫 저장소 | 지원되지 않음 | 가상 머신 디스크는 콜드 및 핫 저장소에서 지원되지 않습니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 스토리지 계정에 대한 가상 네트워크 액세스를 제한하는 경우 ['신뢰할 수 있는 Microsoft 서비스를 허용'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)해야 합니다.
범용 V2 저장소 계정(핫 및 쿨 계층 모두) | 아니요 | 범용 V1 Storage 계정에 비해 상당한 트랜잭션 비용 증가

>[!IMPORTANT]
> [Linux](../virtual-machines/linux/disk-scalability-targets.md) 또는 [Windows](../virtual-machines/windows/disk-scalability-targets.md) 가상 머신에 대한 VM 디스크 확장성 및 성능 목표를 확인하여 성능 문제를 피해야 합니다. 기본 설정을 따르는 경우 Site Recovery가 원본 구성에 따라 필요한 디스크 및 저장소 계정을 만듭니다. 사용자 고유의 설정을 사용자 지정하고 선택하는 경우 소스 VM의 디스크 확장성 및 성능 목표를 준수하는지 확인합니다.

## <a name="azure-site-recovery-limits-to-replicate-data-change-rates"></a>복제 데이터 변경률에 대한 Azure Site Recovery 제한
다음 테이블에는 Azure Site Recovery 제한이 제공됩니다. 이러한 한도는 테스트를 기반으로 하지만 모든 가능한 애플리케이션 I/O 조합을 다룰 수는 없습니다. 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다. 디스크 데이터 변동별 및 가상 머신 데이터 변동별 고려해야 할 두 가지 제한 사항이 있습니다.
예를 들어 아래 테이블에서 프리미엄 P20 디스크를 보면 Site Recovery는 VM당 25MB/s 총 변동 제한으로 인해 VM당 이러한 최대 5개의 디스크로 디스크당 5MB/s 변동을 처리할 수 있습니다.

**복제 저장소 대상** | **평균 원본 디스크 I/O 크기** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB  | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |  디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |10MB/초 | 디스크당 842GB
## <a name="replicated-machines---networking"></a>복제된 컴퓨터 - 네트워킹
**구성** | **지원** | **세부 정보**
--- | --- | ---
NIC | 특정 Azure VM 크기에 대해 지원되는 최대 수 | 장애 조치(failover) 동안 VM이 만들어지면 NIC가 만들어집니다.<br/><br/> 장애 조치(failover) VM의 NIC 수는 복제를 사용하도록 설정한 당시에 원본 VM의 NIC 수에 따라 다릅니다. 복제를 사용하도록 설정한 후에 NIC를 추가하거나 제거하는 경우 장애 조치(failover) 후 복제된 VM의 NIC 수에는 영향을 주지 않습니다.
인터넷 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결합니다.
내부 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결합니다.
공용 IP 주소 | 지원됨 | 기존 공용 IP 주소를 NIC에 연결합니다. 또는 공용 IP 주소를 만들고 복구 계획에서 Azure Automation 스크립트를 사용하여 NIC에 연결합니다.
NIC의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 NIC에 연결합니다.
서브넷의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 서브넷에 연결합니다.
예약된(고정) IP 주소 | 지원됨 | 원본 VM의 NIC에 고정 IP 주소가 있고 대상 서브넷에서 동일한 IP 주소를 사용할 수 있는 경우 해당 IP가 장애 조치(Failover)된 VM에 할당됩니다.<br/><br/> 대상 서브넷에서 동일한 IP 주소를 사용할 수 없는 경우 서브넷의 사용 가능한 IP 주소 중 하나가 이 VM용으로 예약됩니다.<br/><br/> **복제된 항목** > **설정** > **계산 및 네트워크** > **네트워크 인터페이스**에서 고정 IP 주소 및 서브넷을 지정할 수도 있습니다.
동적 IP 주소 | 지원됨 | 원본의 NIC에 동적 IP 주소가 있는 경우 장애 조치(failover)된 VM의 NIC도 기본적으로 동적으로 설정됩니다.<br/><br/> 필요한 경우 이 주소를 고정 IP 주소로 수정할 수 있습니다.
Traffic Manager     | 지원됨 | 트래픽이 평소에는 원본 지역의 엔드포인트로 라우팅되고 장애 조치(Failover) 시에는 대상 지역의 엔드포인트로 라우팅되도록 Traffic Manager를 미리 구성할 수 있습니다.
Azure DNS | 지원됨 |
사용자 지정 DNS  | 지원됨 |
인증되지 않은 프록시 | 지원됨 | [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.    
인증된 프록시 | 지원되지 않음 | VM에서 아웃바운드 연결에 인증된 프록시를 사용하는 경우 Azure Site Recovery를 사용하여 VM을 복제할 수 없습니다.    
온-프레미스를 사용하는 사이트 간 VPN(ExpressRoute 사용 또는 사용 안 함)| 지원됨 | Site Recovery 트래픽이 온-프레미스로 라우팅되지 않도록 UDR 및 NSG가 구성되어 있는지 확인합니다. [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.  
VNet 간 연결 | 지원됨 | [네트워킹 지침 문서](site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.  
Virtual Network 서비스 엔드포인트 | 지원됨 | 스토리지 계정에 대한 가상 네트워크 액세스를 제한하는 경우 신뢰할 수 있는 Microsoft 서비스가 스토리지 계정에 액세스할 수 있는지 확인합니다.
가속 네트워킹 | 지원됨 | 소스 VM에서 가속 네트워킹을 사용하도록 설정해야 합니다. [자세히 알아보기](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>다음 단계
- [Azure VM 복제를 위한 네트워킹 지침](site-recovery-azure-to-azure-networking-guidance.md)에 대해 자세히 알아봅니다.
- [Azure VM을 복제](site-recovery-azure-to-azure.md)하여 재해 복구를 배포합니다.
