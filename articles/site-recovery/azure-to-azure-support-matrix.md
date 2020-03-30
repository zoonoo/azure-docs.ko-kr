---
title: Azure 사이트 복구를 사용 하 고 Azure VM 재해 복구에 대 한 지원 매트릭스
description: Azure 사이트 복구를 사용 하 여 보조 지역에 Azure VM 재해 복구에 대 한 지원을 요약 합니다.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: 0b4a654093e0842e66e1f8b0924edfa6c9c215e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276651"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure 지역 간의 Azure VM 재해 복구에 대한 지원 매트릭스

이 문서에서는 [Azure 사이트](site-recovery-overview.md) 복구 서비스를 사용하여 한 Azure 지역에서 다른 Azure 지역으로 Azure VM의 재해 복구에 대한 지원 및 필수 구성 조건을 요약합니다.


## <a name="deployment-method-support"></a>배포 방법 지원 여부

**배포** |  **지원**
--- | ---
**Azure 포털** | 지원됩니다.
**Powershell** | 지원됩니다. [자세히 알아보기](azure-to-azure-powershell.md)
**나머지 API** | 지원됩니다.
**Cli** | 현재 지원되지 않음


## <a name="resource-support"></a>리소스 지원

**리소스 작업** | **세부 정보**
--- | ---
**리소스 그룹 간에 볼트 이동** | 지원되지 않음
**리소스 그룹 간에 계산/스토리지/네트워크 리소스 이동** | 지원되지 않습니다.<br/><br/> VM 복제 후 VM 또는 스토리지/네트워크와 같은 관련 구성 요소를 이동하는 경우 VM에 대한 복제를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다.
**재해 복구를 위해 한 구독에서 다른 구독으로 Azure VM 복제** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**지원되는 지역별 클러스터 내의 여러 지역 간에 VM 마이그레이션(구독 내/구독 간)** | 동일한 Azure Active Directory 테넌트 내에서 지원됩니다.
**동일한 지역 내에서 VM 마이그레이션** | 지원되지 않습니다.

## <a name="region-support"></a>지역 지원

동일한 지리적 클러스터 내의 두 지역 간에 VM을 복제 및 복구할 수 있습니다. 지리적 클러스터는 데이터 대기 시간 및 주권에 유의해서 정의해야 합니다.


**지리적 클러스터** | **Azure 지역**
-- | --
America | 캐나다 동부, 캐나다 중부, 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2, 미국 중부, 미국 중북부
유럽 | 영국 서부, 영국 남부, 북유럽, 서유럽, 프랑스 중부, 프랑스 남부, 남아프리카 서부, 남아프리카 북부, 노르웨이 동부, 노르웨이 서부
아시아 | 남인도, 인도 중부, 서인도, 동남아시아, 동아시아, 일본 동부, 일본 서부, 한국 중부, 한국, 아랍에미리트 중부, 아랍에미리트 북부
오스트레일리아    | 오스트레일리아 동부, 오스트레일리아 남동부, 오스트레일리아 중부, 오스트레일리아 중부 2
Azure Government    | US Gov 버지니아, US Gov 아이오와, US Gov 애리조나, US Gov 텍사스, US DoD 동부, US DoD 중부
독일    | 독일 중부, 독일 북동부
중국 | 중국 동부, 중국 북부, 중국 북부 2, 중국 동부 2
국내 재해 복구를 위해 예약된 제한된 지역 |독일 북부 는 독일 서중부, 스위스 서부는 스위스 북부, 프랑스 남부는 프랑스 중부 고객을 위해 예약

>[!NOTE]
>
> - **브라질 남부의**경우 미국 중남부, 미국 중서부, 미국 동부, 미국 동부 2, 미국 서부, 미국 서부 2 및 미국 중북부 지역으로 복제및 장애 조치할 수 있습니다.
> - 브라질 남부는 VM이 사이트 복구를 사용하여 복제할 수 있는 소스 지역으로만 사용할 수 있습니다. 대상 지역으로 작동할 수 없습니다. 이는 지리적 거리로 인한 대기 시간 문제 때문입니다. 브라질 남부에서 대상에 대한 소스 지역으로 장애 조치하는 경우 대상 지역에서 브라질 남부로 장애 조치(failback)가 지원됩니다.
> - 적절한 액세스 권한이 있는 리전 내에서 작업할 수 있습니다.
> - 자격 증명 모음을 만들 려는 리전이 표시되지 않는 경우 구독에 해당 리전의 리소스를 만들 수 있는 액세스 권한이 있는지 확인합니다.
> - 복제를 사용하도록 설정할 때 지리적 클러스터 내에 영역이 표시되지 않는 경우 구독에 해당 리전에서 VM을 만들 수 있는 권한이 있는지 확인합니다.



## <a name="cache-storage"></a>캐시 스토리지

이 표에서는 복제하는 동안 Site Recovery에서 사용하는 캐시 스토리지 계정에 대한 지원을 요약해서 보여 줍니다.

**설정** | **지원** | **세부 정보**
--- | --- | ---
범용 V2 스토리지 계정(핫 및 쿨 계층) | 지원됨 | V2의 트랜잭션 비용이 V1 저장소 계정보다 상당히 높기 때문에 GPv2를 사용하지 않는 것이 좋습니다.
Premium Storage | 지원되지 않음 | 표준 저장소 계정은 비용을 최적화하는 데 도움이 되는 캐시 저장소에 사용됩니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 방화벽 지원 캐시 스토리지 계정 또는 대상 스토리지 계정을 사용하는 경우 ['신뢰할 수 있는 Microsoft 서비스 허용'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)을 선택해야 합니다.<br></br>또한 원본 Vnet의 하나 이상의 서브넷에 대한 액세스를 허용해야 합니다.


## <a name="replicated-machine-operating-systems"></a>복제된 컴퓨터 운영 체제

Site Recovery는 이 섹션에 나열된 운영 체제를 실행하는 Azure VM의 복제를 지원합니다. 이미 복제된 컴퓨터가 이후에 다른 주 커널로 업그레이드(또는 다운그레이드)된 경우 복제를 비활성화하고 업그레이드 후 복제를 다시 활성화해야 합니다.

### <a name="windows"></a>Windows


**운영 체제** | **세부 정보**
--- | ---
Windows Server 2019 | 서버 코어, 데스크톱 환경이 있는 서버용으로 지원됩니다.
Windows Server 2016  | 지원되는 서버 코어, 데스크톱 환경이 있는 서버.
Windows Server 2012 R2 | 지원됩니다.
Windows Server 2012 | 지원됩니다.
SP1/SP2를 갖춘 Windows 서버 2008 R2 | 지원됩니다.<br/><br/> Azure VM에 대한 모빌리티 서비스 확장의 버전 [9.30에서](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) Windows Server 2008 R2 SP1/SP2를 실행하는 컴퓨터에 Windows [서비스 스택 업데이트(SSU)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트를](https://support.microsoft.com/help/4474419) 설치해야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며 SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항에](https://aka.ms/SHA-2KB)대해 자세히 알아보십시오.
Windows 10(x64) | 지원됩니다.
윈도우 8.1 (x64) | 지원됩니다.
윈도우 8 (x64) | 지원됩니다.
윈도우 7 (x64) 이후 SP1와 | Azure VM에 대한 모빌리티 서비스 확장의 버전 [9.30에서](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) SP1을 사용하여 Windows 7을 실행하는 컴퓨터에 Windows [서비스 스택 업데이트(SSU)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트를](https://support.microsoft.com/help/4474419) 설치해야 합니다.  SHA-1은 2019년 9월부터 지원되지 않으며 SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항에](https://aka.ms/SHA-2KB)대해 자세히 알아보십시오.



#### <a name="linux"></a>Linux

**운영 체제** | **세부 정보**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [8.0, 8.1](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu 서버 사용 암호 기반 인증 및 로그인, 클라우드 VM을 구성 하는 클라우드 init 패키지, 장애 조치 (cloudinit 구성에 따라) 장애 조치에서 사용 안 함 암호 기반 로그인 있을 수 있습니다. Azure 포털에서 VM을 통해 실패한 경우 지원 > 문제 해결 > 설정 메뉴에서 암호를 재설정하여 가상 컴퓨터에서 암호 기반 로그인을 다시 사용할 수 있습니다.
우분투 18.04 LTS 서버 | [지원되는 커널 버전](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [지원되는 커널 버전](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 및 15 SP1. [(지원되는 커널 버전)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> 복제 컴퓨터를 SP3에서 SP4로 업그레이드하는 것은 지원되지 않습니다. 복제된 컴퓨터를 업그레이드한 경우 복제를 사용하지 않도록 설정하고 업그레이드 후에 다시 사용하도록 설정해야 합니다.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> 레드햇 호환 커널 또는 깨지지 않는 엔터프라이즈 커널 릴리스 3, 4 & 5 (UEK3, UEK4, UEK5) 실행


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Ubuntu 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
14.04 LTS | 9.31 | 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
14.04 LTS | 9.30 | 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
14.04 LTS | 9.29 | 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
|||
16.04 LTS | 9.32 | 4.4.0-21-제네릭 ~ 4.4.0-171-제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-74 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1066-azure|
16.04 LTS | 9.31 | 4.4.0-21-제네릭 ~ 4.4.0-170 제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-72 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1063-azure|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-제네릭 ~ 4.4.0-166-제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-66 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1061-azure|
16.04 LTS | 9.29 | 4.4.0-21-제네릭 ~ 4.4.0-164-제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-64 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1059-azure|
|||
18.04 LTS | 9.32| 4.15.0-20 제네릭 - 4.15.0-74 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-37-제네릭 </br> 5.3.0-19-제네릭 ~ 5.3.0-24-제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1028-azure </br> 5.3.0-1007-azure에서 5.3.0-1009-azure|
18.04 LTS | 9.31| 4.15.0-20 제네릭 - 4.15.0-72 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-37-제네릭 </br> 5.3.0-19-제네릭 ~ 5.3.0-24-제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20 제네릭 ~ 4.15.0-66 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-32-제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1023-azure|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20 제네릭 ~ 4.15.0-64 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-29 제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1020-azure|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure virtual Machines에 대해 지원되는 Debian 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 ~ 3.16.0-10 amd64, 4.9.0-0.bpo.4-amd64 ~ 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 ~ 3.16.0-10 amd64, 4.9.0-0.bpo.4-amd64 ~ 4.9.0-0.bpo.9-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 가상 머신에 대해 지원되는 SUSE Linux Enterprise Server 12 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | 9.32 | 모든 [주식 SUSE 12 SP1, SP2, SP3, SP4 커널이 지원됩니다.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure,</br>4.12.14-6.3-azure에서 4.12.14-6.34-azure  |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | 9.31 | 모든 [주식 SUSE 12 SP1, SP2, SP3, SP4 커널이 지원됩니다.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure,</br>4.12.14-6.3-azure에서 4.12.14-6.29-azure  |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | 9.30 | 모든 [주식 SUSE 12 SP1, SP2, SP3, SP4 커널이 지원됩니다.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure,</br>4.12.14-6.3-azure에서 4.12.14-6.29-azure  |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | 9.29 | 모든 [주식 SUSE 12 SP1, SP2, SP3, SP4 커널이 지원됩니다.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure에서 4.4.180-4.31-azure,</br>4.12.14-6.3-azure에서 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 가상 머신에 대한 지원되는 SUSE Linux 엔터프라이즈 서버 15 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
수지 리눅스 엔터프라이즈 서버 15 및 15 SP1 | 9.32 | 모든 [재고 SUSE 15 및 15 커널이 지원됩니다.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15)</br></br> 4.12.14-5.5-azure에서 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>복제된 컴퓨터 - Linux 파일 시스템/게스트 스토리지

* 파일 시스템: 내항3, 내(외압), XFS, BTRFS
* 볼륨 관리자: LVM2
* 다중 경로 소프트웨어: 디바이스 매퍼


## <a name="replicated-machines---compute-settings"></a>복제된 머신 - 컴퓨팅 설정

**설정** | **지원** | **세부 정보**
--- | --- | ---
크기 | CPU 코어가 2개 이상이고 1GB 이상의 RAM이 탑재된 모든 Azure VM | [Azure Virtual Machine 크기](../virtual-machines/windows/sizes.md)를 확인합니다.
가용성 집합 | 지원됨 | 기본 옵션을 사용하여 Azure VM에 대한 복제를 사용하도록 설정하면 원본 지역 설정에 따라 가용성 집합이 자동으로 만들어집니다. 이러한 설정을 수정할 수 있습니다.
가용성 영역 | 지원됨 |
HUB(하이브리드 사용 혜택) | 지원됨 | 원본 VM에 활성 HUB 라이선스가 있는 경우 테스트 장애 조치(failover) 또는 장애 조치(failover)된 VM에서도 HUB 라이선스를 사용합니다.
가상 머신 크기 집합 | 지원되지 않음 |
Azure 갤러리 이미지 - Microsoft 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Azure 갤러리 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
사용자 지정 이미지 - 타사 게시 | 지원됨 | VM이 지원되는 운영 체제에서 실행되는 경우에 지원됨
Site Recovery를 사용하여 마이그레이션된 VM | 지원됨 | VMware VM 또는 물리적 컴퓨터가 Site Recovery를 사용하여 Azure에 마이그레이션되면 컴퓨터에서 실행되는 이전 버전의 모바일 서비스를 제거하고 컴퓨터를 다시 시작한 후 다른 Azure 지역에 복제해야 합니다.
RBAC 정책 | 지원되지 않음 | VM의 역할 기반 액세스 제어(RBAC) 정책은 대상 지역의 장애 조치 VM에 복제되지 않습니다.
확장 | 지원되지 않음 | 확장은 대상 지역의 장애 조치 VM에 복제되지 않습니다. 장애 조치 후 수동으로 설치해야 합니다.

## <a name="replicated-machines---disk-actions"></a>복제된 컴퓨터 - 디스크 작업

**작업** | **세부 정보**
-- | ---
복제된 VM에서 디스크 크기 조정 | 장애 조치 전에 소스 VM에서 지원됩니다. 복제를 사용하지 않도록 설정/다시 사용할 필요가 없습니다.<br/><br/> 장애 조치 후 소스 VM을 변경하면 변경 내용이 캡처되지 않습니다.<br/><br/> 장애 조치 후 Azure VM의 디스크 크기를 변경하면 변경 내용이 사이트 복구에 의해 캡처되지 않으며 장애 복구는 원래 VM 크기로 변경됩니다.
복제된 VM에 디스크 추가 | 지원됨

## <a name="replicated-machines---storage"></a>복제된 컴퓨터 - 스토리지

이 표에서는 Azure VM OS 디스크, 데이터 디스크 및 임시 디스크에 대한 지원을 요약해서 표시합니다.

- 성능 문제를 방지하려면 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 및 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM의 VM 디스크 제한 및 목표를 확인하는 것이 중요합니다.
- 기본 설정을 사용하여 배포하는 경우 Site Recovery는 원본 설정에 따라 디스크 및 스토리지 계정을 자동으로 만듭니다.
- 사용자 지정하는 경우 지침을 따릅니다.

**구성 요소** | **지원** | **세부 정보**
--- | --- | ---
OS 디스크 최대 크기 | 2048GB | VM 디스크에 대해 [자세히 알아봅니다](../virtual-machines/windows/managed-disks-overview.md).
임시 디스크 | 지원되지 않음 | 임시 디스크는 항상 복제에서 제외됩니다.<br/><br/> 임시 디스크에는 영구 데이터를 저장하지 마세요. [자세히 알아봅니다](../virtual-machines/windows/managed-disks-overview.md).
데이터 디스크 최대 크기 | 관리 디스크의 경우 8192GB<br></br>관리되지 않는 디스크의 경우 4095GB|
데이터 디스크 최소 크기 | 관리되지 않는 디스크에 대한 제한은 없습니다. 관리 디스크의 경우 2GB |
데이터 디스크 최대 수 | 특정 Azure VM 크기에 대한 지원에 따라 최대 64개 | VM 크기에 대해 [자세히 알아봅니다](../virtual-machines/windows/sizes.md).
데이터 디스크 변경 비율 | Premium Storage는 디스크당 최대 10MBps입니다. Standard Storage는 디스크당 최대 2MBps입니다. | 디스크의 평균 데이터 변경률이 계속해서 최대값보다 큰 경우 복제가 처리되지 않습니다.<br/><br/>  그러나 최대값을 산발적으로 초과하는 경우 복제가 처리될 수 있지만 복구 지점이 약간 지연될 수 있습니다.
데이터 디스크 - Standard Storage 계정 | 지원됨 |
데이터 디스크 - Premium Storage 계정 | 지원됨 | VM의 디스크가 Premium Storage 계정과 표준 스토리지 계정에 분산된 경우 대상 지역의 스토리지를 동일하게 구성하기 위해 각 디스크에 대해 서로 다른 대상 스토리지 계정을 선택할 수 있습니다.
Managed Disk - Standard | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
Managed Disk - Premium | Azure Site Recovery가 지원되는 Azure 지역에서 지원됩니다. |
표준 SSD | 지원됨 |
중복 | LRS 및 GRS가 지원됩니다.<br/><br/> ZRS는 지원되지 않습니다.
콜드 및 핫 스토리지 | 지원되지 않음 | VM 디스크는 콜드 및 핫 스토리지에서 지원되지 않습니다.
스토리지 공간 | 지원됨 |
미사용 암호화(SSE) | 지원됨 | SSE은 스토리지 계정의 기본 설정입니다.
미사용 암호화(CMK) | 지원됨 | 관리 디스크에 대해 소프트웨어 및 HSM 키가 모두 지원됩니다.
Windows OS용 ADE(Azure Disk Encryption) | 관리 디스크가 있는 VM에 대해 지원됩니다. | 관리되지 않는 디스크를 사용하는 VM은 지원되지 않습니다. <br/><br/> HSM으로 보호된 키는 지원되지 않습니다. |
Linux OS용 ADE(Azure Disk Encryption) | 관리 디스크가 있는 VM에 대해 지원됩니다. | 관리되지 않는 디스크를 사용하는 VM은 지원되지 않습니다. <br/><br/> HSM으로 보호된 키는 지원되지 않습니다. |
핫 추가    | 지원됨 | 복제된 Azure VM에 추가하는 데이터 디스크에 대한 복제를 사용하도록 설정하는 것은 관리되는 디스크를 사용하는 VM에서 지원됩니다.
핫 제거 디스크    | 지원되지 않음 | VM에서 데이터 디스크를 제거하는 경우 복제를 사용하지 않도록 설정하고 VM에 대해 복제를 다시 활성화해야 합니다.
디스크 제외 | 지지도. [구성하려면 PowerShell을](azure-to-azure-exclude-disks.md) 사용해야 합니다. |    임시 디스크는 기본적으로 제외됩니다.
직접 스토리지 공간  | 크래시 일관성이 있는 복구 지점을 지원합니다. 애플리케이션 일관성이 있는 복구 지점은 지원되지 않습니다. |
스케일 아웃 파일 서버  | 크래시 일관성이 있는 복구 지점을 지원합니다. 애플리케이션 일관성이 있는 복구 지점은 지원되지 않습니다. |
DRBD | DRBD 설정의 일부인 디스크는 지원되지 않습니다. |
LRS | 지원됨 |
GRS | 지원됨 |
RA-GRS | 지원됨 |
ZRS | 지원되지 않음 |
콜드 및 핫 스토리지 | 지원되지 않음 | 가상 머신 디스크는 콜드 및 핫 스토리지에서 지원되지 않습니다.
가상 네트워크의 Azure Storage 방화벽  | 지원됨 | 저장소 계정에 대한 가상 네트워크 액세스를 제한하는 경우 [신뢰할 수 있는 Microsoft 서비스 허용을](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)사용하도록 설정합니다.
범용 V2 스토리지 계정(핫 및 쿨 계층 모두) | 지원됨 | 범용 V1 Storage 계정에 비해 상당한 트랜잭션 비용 증가
2세대(UEFI 부츠) | 지원됨

>[!IMPORTANT]
> 성능 문제를 방지하려면 [Linux](../virtual-machines/linux/disk-scalability-targets.md) 또는 [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM의 VM 디스크 확장성 및 성능 목표를 준수해야 합니다. 기본 설정을 사용하는 경우 사이트 복구는 원본 구성에 따라 필요한 디스크 및 저장소 계정을 만듭니다. 사용자 지정 하고 자신의 설정을 선택 하는 경우, 소스 VM에 대 한 디스크 확장성 및 성능 목표를 따릅니다.

## <a name="limits-and-data-change-rates"></a>한도 및 데이터 변경 률

다음 표에는 사이트 복구 제한이 요약됩니다.

- 이러한 제한은 테스트를 기반으로 하지만 가능한 모든 응용 프로그램 I/O 조합을 다루지는 않습니다.
- 실제 결과는 앱 I/O 믹스에 따라 달라질 수 있습니다.
- 디스크 데이터 변동 및 가상 시스템 데이터 변동당 고려해야 할 두 가지 제한점이 있습니다.

**스토리지 대상** | **평균 소스 디스크 I/O** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20 MB/s | 디스크당 1684GB

## <a name="replicated-machines---networking"></a>복제된 컴퓨터 - 네트워킹
**설정** | **지원** | **세부 정보**
--- | --- | ---
NIC | 특정 Azure VM 크기에 대해 지원되는 최대 수 | 장애 조치(failover) 동안 VM이 만들어지면 NIC가 만들어집니다.<br/><br/> 장애 조치(failover) VM의 NIC 수는 복제를 사용하도록 설정한 당시에 원본 VM의 NIC 수에 따라 다릅니다. 복제를 사용하도록 설정한 후에 NIC를 추가하거나 제거하는 경우 장애 조치(failover) 후 복제된 VM의 NIC 수에는 영향을 주지 않습니다. 또한 장애 조치 후 NIC의 순서가 원래 순서와 동일하지 는 않습니다.
인터넷 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결합니다.
내부 부하 분산 장치 | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 미리 구성된 부하 분산 장치를 연결합니다.
공용 IP 주소 | 지원됨 | 기존 공용 IP 주소를 NIC에 연결합니다. 또는 공용 IP 주소를 만들고 복구 계획에서 Azure Automation 스크립트를 사용하여 NIC에 연결합니다.
NIC의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 NIC에 연결합니다.
서브넷의 NSG | 지원됨 | 복구 계획에서 Azure Automation 스크립트를 사용하여 NSG를 서브넷에 연결합니다.
예약된(고정) IP 주소 | 지원됨 | 원본 VM의 NIC에 고정 IP 주소가 있고 대상 서브넷에서 동일한 IP 주소를 사용할 수 있는 경우 해당 IP가 장애 조치(Failover)된 VM에 할당됩니다.<br/><br/> 대상 서브넷에서 동일한 IP 주소를 사용할 수 없는 경우 서브넷의 사용 가능한 IP 주소 중 하나가 이 VM용으로 예약됩니다.<br/><br/> **복제된 항목에서** > **Settings** > **Compute and Network** > 고정 IP 주소 및 서브넷을 지정할 수도**있습니다.**
동적 IP 주소 | 지원됨 | 원본의 NIC에 동적 IP 주소가 있는 경우 장애 조치(failover)된 VM의 NIC도 기본적으로 동적으로 설정됩니다.<br/><br/> 필요한 경우 이 주소를 고정 IP 주소로 수정할 수 있습니다.
여러 IP 주소 | 지원되지 않음 | IP 주소가 여러 개인 NIC가 있는 VM을 장애 조치하면 원본 지역의 NIC의 기본 IP 주소만 유지됩니다. 여러 IP 주소를 할당하려면 [복구 계획에](recovery-plan-overview.md) VM을 추가하고 스크립트를 첨부하여 계획에 추가 IP 주소를 할당하거나 장애 조치 후 수동으로 또는 스크립트로 변경할 수 있습니다.
Traffic Manager     | 지원됨 | 트래픽이 평소에는 원본 지역의 엔드포인트로 라우팅되고 장애 조치(Failover) 시에는 대상 지역의 엔드포인트로 라우팅되도록 Traffic Manager를 미리 구성할 수 있습니다.
Azure DNS | 지원됨 |
사용자 지정 DNS    | 지원됨 |
인증되지 않은 프록시 | 지원됨 | [자세히 알아보기](site-recovery-azure-to-azure-networking-guidance.md)
인증된 프록시 | 지원되지 않음 | VM에서 아웃바운드 연결에 인증된 프록시를 사용하는 경우 Azure Site Recovery를 사용하여 VM을 복제할 수 없습니다.
온-프레미스에 대한 VPN 사이트 간 연결<br/><br/>(익스프레스루트 유무에 관계없이)| 지원됨 | 사이트 복구 트래픽이 온-프레미스로 라우팅되지 않도록 DUD 및 NSG가 구성되었는지 확인합니다. [자세히 알아보기](site-recovery-azure-to-azure-networking-guidance.md)
VNet 간 연결    | 지원됨 | [자세히 알아보기](site-recovery-azure-to-azure-networking-guidance.md)
Virtual Network 서비스 엔드포인트 | 지원됨 | 스토리지 계정에 대한 가상 네트워크 액세스를 제한하는 경우 신뢰할 수 있는 Microsoft 서비스가 스토리지 계정에 액세스할 수 있는지 확인합니다.
가속된 네트워킹 | 지원됨 | 원본 VM에서 가속 네트워킹을 사용하도록 설정해야 합니다. [자세히 알아봅니다](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>다음 단계
- Azure VM 복제에 대한 [네트워킹 지침을](site-recovery-azure-to-azure-networking-guidance.md) 읽어보십시오.
- [Azure VM을 복제](site-recovery-azure-to-azure.md)하여 재해 복구를 배포합니다.
