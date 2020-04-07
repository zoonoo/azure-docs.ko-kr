---
title: Azure 사이트 복구에서 VMware/물리적 재해 복구에 대한 지원 매트릭스
description: Azure 사이트 복구를 사용하여 VMware VM 및 물리적 서버의 재해 복구에 대한 지원을 Azure에 요약합니다.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: fbd5d87b219cbb482569dc5e45adc9c81181670c
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672447"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM 또는 물리적 서버와 Azure 간 재해 복구를 위한 지원 매트릭스

이 문서에서는 [Azure 사이트](site-recovery-overview.md)복구를 사용하여 Azure에 VMware VM 및 물리적 서버의 재해 복구를 위해 지원되는 구성 요소 및 설정을 요약합니다.

- VMware VM/물리적 서버 재해 복구 아키텍처에 대해 [자세히 알아봅니다.](vmware-azure-architecture.md)
- 재해 [복구를](tutorial-prepare-azure.md) 시도하려면 자습서를 따르십시오.

## <a name="deployment-scenarios"></a>배포 시나리오

**시나리오** | **세부 정보**
--- | ---
VMware VM의 재해 복구 | 온-프레미스 VMware VM을 Azure로 복제. Azure 포털에서 또는 [PowerShell](vmware-azure-disaster-recovery-powershell.md)을 사용하여 이 시나리오를 배포할 수 있습니다.
물리적 서버의 재해 복구 | 온-프레미스 Windows/Linux 실제 서버를 Azure로 복제 Azure Portal에서 이 시나리오를 배포할 수 있습니다.

## <a name="on-premises-virtualization-servers"></a>온-프레미스 가상화 서버

**Server** | **요구 사항** | **세부 정보**
--- | --- | ---
vCenter Server | 버전 6.7, 6.5, 6.0 또는 5.5 | 재해 복구 배포에 vCenter 서버를 사용하는 것이 좋습니다.
vSphere 호스트 | 버전 6.7, 6.5, 6.0 또는 5.5 | vSphere 호스트와 vCenter 서버가 프로세스 서버와 동일한 네트워크에 있는 것이 좋습니다. 기본적으로 프로세스 서버는 구성 서버에서 실행됩니다. [자세히 알아보기](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery 구성 서버

구성 서버는 구성 서버, 프로세스 서버 및 마스터 대상 서버를 포함하여 Site Recovery 구성 요소를 실행하는 온-프레미스 컴퓨터입니다.

- VMware VM의 경우 OVF 템플릿을 다운로드하여 VMware VM을 만들어 구성 서버를 설정합니다.
- 실제 서버의 경우 구성 서버 컴퓨터를 수동으로 설정합니다.

**구성 요소** | **요구 사항**
--- |---
CPU 코어 | 8
RAM | 16GB
디스크 수 | 3개의 디스크<br/><br/> 디스크에는 OS 디스크, 프로세스 서버 캐시 디스크, 보존 드라이브(장애 복구용)가 포함됩니다.
사용 가능한 디스크 공간 | 프로세스 서버 캐시에 대한 600GB의 공간입니다.
사용 가능한 디스크 공간 | 리텐션 드라이브를 위한 600GB의 공간.
운영 체제  | 윈도우 서버 2012 R2, 또는 윈도우 서버 2016 데스크톱 경험 <br/><br> 장애 조치(failback)에 이 어플라이언스의 기본 제공 마스터 대상을 사용하려는 경우 OS 버전이 복제된 항목과 동일하거나 더 높은지 확인합니다.|
운영 체제 로케일 | 미국 영어(en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | 구성 서버 버전 [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 이상에는 필요하지 않습니다.
Windows Server 역할 | 활성 디렉터리 도메인 서비스를 사용하도록 설정하지 마십시오. 인터넷 정보 서비스(IIS) 또는 하이퍼-V.
그룹 정책| - 명령 프롬프트에 대한 액세스 방지 <br/> - 레지스트리 편집 도구에 대한 액세스 방지 <br/> - 파일 첨부를 위한 트러스트 논리 <br/> - 스크립트 실행 켜기 <br/> - [자세한 정보](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 다음을 확인합니다.<br/><br/> - 기존의 기본 웹 사이트가 없습니다 <br/> - [익명 인증](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) 사용 <br/> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 설정 사용  <br/> - 포트 443에서 수신 대기하는 기존의 웹 사이트/앱 없음<br/>
NIC 유형 | VMXNET3(VMware VM으로 배포될 경우)
IP 주소 유형 | 정적
포트 | 443 제어 채널 오케스트레이션에 사용<br/>데이터 전송용 9443

## <a name="replicated-machines"></a>복제된 컴퓨터

Site Recovery는 지원되는 컴퓨터에서 실행되는 모든 워크로드의 복제를 지원합니다.

> [!Note]
> 다음 표에는 BIOS 부팅이 있는 컴퓨터에 대한 지원이 나열되어 있습니다. UEFI 기반 기계에 대한 지원은 [저장소](#storage) 섹션을 참조하십시오.

**구성 요소** | **세부 정보**
--- | ---
컴퓨터 설정 | Azure로 복제하는 컴퓨터는 [Azure 요구 사항](#azure-vm-requirements)을 충족해야 합니다.
머신 워크로드 | Site Recovery는 지원되는 컴퓨터에서 실행되는 모든 워크로드의 복제를 지원합니다. [자세히 알아보기](https://aka.ms/asr_workload).
컴퓨터 이름 | 컴퓨터의 표시 이름이 Azure 예약 [리소스 이름에](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name) 속하지 않는지 확인합니다.<br/><br/> 논리 볼륨 이름은 대/소문자를 구분하지 않습니다. 장치의 두 볼륨에 이름이 같지 않은지 확인합니다. 예: 이름이 "voLUME1", "volume1"인 볼륨은 Azure 사이트 복구를 통해 보호할 수 없습니다.
Windows Server 2019 | 업데이트 [롤업 34(모빌리티](https://support.microsoft.com/help/4490016) 서비스의 버전 9.22 버전)에서 지원됩니다.
윈도우 서버 2016 64 비트 | 서버 코어, 데스크톱 환경이 있는 서버용으로 지원됩니다.
윈도우 서버 2012 R2 / 윈도우 서버 2012 | 지원됩니다.
이후 SP1와 윈도우 서버 2008 R2. | 지원됩니다.<br/><br/> 모빌리티 서비스 에이전트의 버전 [9.30에서](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) SP1 이상으로 Windows 2008 R2를 실행하는 컴퓨터에 설치된 [SSU(스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트가](https://support.microsoft.com/help/4474419) 필요합니다. SHA-1은 2019년 9월부터 지원되지 않으며 SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항에](https://aka.ms/SHA-2KB)대해 자세히 알아보십시오.
SP2 이상 (64 비트 / 32 비트)와 윈도우 서버 2008 |  마이그레이션에만 지원됩니다. [자세히 알아보기](migrate-tutorial-windows-server-2008.md).<br/><br/> 모빌리티 서비스 에이전트의 버전 [9.30에서](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) Windows 2008 SP2 컴퓨터에 설치된 [SSU(스택 업데이트)](https://support.microsoft.com/help/4493730) 및 [SHA-2 업데이트가](https://support.microsoft.com/help/4474419) 필요합니다. ISHA-1은 2019년 9월부터 지원되지 않으며 SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항에](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)대해 자세히 알아보십시오.
Windows 10, Windows 8.1, Windows 8 | 지원됩니다.
SP1 64 비트와 윈도우 7 | 업데이트 [롤업 36(모빌리티](https://support.microsoft.com/help/4503156) 서비스의 버전 9.22 버전)부터 지원됩니다. </br></br> 모빌리티 서비스 에이전트의 [9.30에서](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) Windows 7 SP1 컴퓨터에 설치된 [SSU(스택 업데이트)](https://support.microsoft.com/help/4490628) 및 [SHA-2 업데이트가](https://support.microsoft.com/help/4474419) 필요합니다.  SHA-1은 2019년 9월부터 지원되지 않으며 SHA-2 코드 서명을 사용하도록 설정하지 않으면 에이전트 확장이 예상대로 설치/업그레이드되지 않습니다. [SHA-2 업그레이드 및 요구 사항에](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)대해 자세히 알아보십시오.
Linux | 64비트 시스템만 지원됩니다. 32비트 시스템은 지원되지 않습니다.<br/><br/>모든 Linux 서버에는 [LIS(Linux 통합 서비스) 구성 요소가](https://www.microsoft.com/download/details.aspx?id=55106) 설치되어 있어야 합니다. 테스트 장애 조치(failover/장애 조치)를 테스트한 후 Azure에서 서버를 부팅해야 합니다. LIS 구성 요소가 없는 경우 Azure에서 컴퓨터를 부팅하기 위한 복제를 사용하도록 설정하기 전에 [구성 요소를](https://www.microsoft.com/download/details.aspx?id=55106) 설치해야 합니다. <br/><br/> Site Recovery는 Azure에서 Linux 서버를 실행하도록 장애 조치(failover)를 오케스트레이션합니다. 그러나 Linux 공급 업체 지원 수명 종료에 해당하지 않는 배포 버전만으로 제한될 수 있습니다.<br/><br/> Linux 배포에서는 배포의 부 버전 릴리스/업데이트에 포함된 스톡 커널만 지원됩니다.<br/><br/> 주요 Linux 배포 버전에서 보호된 시스템을 업그레이드하는 것은 지원되지 않습니다. 업그레이드하려면 복제를 사용하지 않도록 설정하고, 운영 체제를 업그레이드한 다음, 복제를 다시 사용하도록 설정합니다.<br/><br/> Azure의 Linux 및 오픈 소스 기술 지원에 대해 [자세히 알아보세요.](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)
리눅스 레드 햇 엔터프라이즈 | 5.2 ~ 5.11</b><br/> 6.1 ~ 6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7,](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) [8.0,](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)8.1 <br/> 6.1-6.10 & Red Hat 엔터프라이즈 Linux 5.2-5.11을 실행하는 서버에는 [Linux 통합 서비스(LIS) 구성 요소가](https://www.microsoft.com/download/details.aspx?id=55106) 사전 설치되어 있지 않습니다. 컴퓨터가 Azure에서 부팅되도록 복제를 사용하도록 설정하기 전에 [구성 요소를](https://www.microsoft.com/download/details.aspx?id=55106) 설치해야 합니다.
Linux: CentOS | 5.2 ~ 5.11</b><br/> 6.1 ~ 6.10</b><br/> 7.0 ~ 7.6<br/> <br/> 8.0 ~ 8.1<br/><br/> CentOS 5.2-5.11 & 6.1-6.10을 실행하는 서버에는 [Linux 통합 서비스(LIS) 구성 요소가](https://www.microsoft.com/download/details.aspx?id=55106) 사전 설치되어 있지 않습니다. 컴퓨터가 Azure에서 부팅되도록 복제를 사용하도록 설정하기 전에 [구성 요소를](https://www.microsoft.com/download/details.aspx?id=55106) 설치해야 합니다.
Ubuntu | 우분투 14.04 LTS 서버 [(검토 지원 커널 버전)](#ubuntu-kernel-versions)<br/><br/>우분투 16.04 LTS 서버 [(검토 지원 커널 버전)](#ubuntu-kernel-versions) </br> 우분투 18.04 LTS 서버 [(검토 지원 커널 버전)](#ubuntu-kernel-versions)
Debian | 데비안 7/데비안 8 [(검토 지원 커널 버전)](#debian-kernel-versions)
SUSE Linux | SUSE 리눅스 엔터프라이즈 서버 12 SP1, SP2, SP3, SP4 [(검토 지원 커널 버전)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE 리눅스 엔터프라이즈 서버 15, 15 SP1 [(검토 지원 커널 버전)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3 또는 SUSE Linux Enterprise Server 11 SP4<br/> 복제된 머신을 SUSE Linux Enterprise Server 11 SP3에서 SP4로 업그레이드하는 것은 지원되지 않습니다. 업그레이드하려면 복제를 사용하지 않도록 설정하고 업그레이드 후 다시 활성화합니다.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> 레드햇 호환 커널 또는 깨지지 않는 엔터프라이즈 커널 릴리스 3, 4 & 5 (UEK3, UEK4, UEK5) 실행

> [!Note]
> 각 Windows 버전에 대해 Azure 사이트 복구는 [LTSC(장기 서비스 채널)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 빌드만 지원합니다.  [현재 반기 채널](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 릴리스는 현재 지원되지 않습니다.

### <a name="ubuntu-kernel-versions"></a>Ubuntu 커널 버전

**지원되는 릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-제네릭 ~ 3.13.0-170-제네릭,<br/>3.16.0-25-generic에서 3.16.0-77-generic<br/>3.19.0-18-generic에서 3.19.0-80-generic<br/>4.2.0-18-generic에서 4.2.0-42-generic<br/>4.4.0-21-제네릭 ~ 4.4.0-148-제네릭,<br/>4.15.0-1023-azure에서 4.15.0-1045-azure |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-제네릭 ~ 4.4.0-171-제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-74 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1066-azure|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-제네릭 ~ 4.4.0-170 제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-72 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1063-azure|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-제네릭 ~ 4.4.0-166-제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-66 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1061-azure|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-제네릭 ~ 4.4.0-164-제네릭,<br/>4.8.0-34-generic에서 4.8.0-58-generic<br/>4.10.0-14-generic에서 4.10.0-42-generic<br/>4.11.0-13-generic에서 4.11.0-14-generic<br/>4.13.0-16-generic에서 4.13.0-45-generic<br/>4.15.0-13 제네릭 - 4.15.0-64 제네릭<br/>4.11.0-1009-azure에서 4.11.0-1016-azure<br/>4.13.0-1005-azure에서 4.13.0-1018-azure <br/>4.15.0-1012-azure에서 4.15.0-1059-azure|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20 제네릭 - 4.15.0-74 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-37-제네릭 </br> 5.3.0-19-제네릭 ~ 5.3.0-24-제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1028-azure </br> 5.3.0-1007-azure에서 5.3.0-1009-azure|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20 제네릭 - 4.15.0-72 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-37-제네릭 </br> 5.3.0-19-제네릭 ~ 5.3.0-24-제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1025-azure </br> 5.3.0-1007-azure|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20 제네릭 ~ 4.15.0-66 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-32-제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1023-azure|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20 제네릭 ~ 4.15.0-62 제네릭 </br> 4.18.0-13 제네릭 - 4.18.0-25-제네릭 </br> 5.0.0-15-제네릭 ~ 5.0.0-27-제네릭 </br> 4.15.0-1009-azure에서 4.15.0-1037-azure </br> 4.18.0-1006-azure에서 4.18.0-1025-azure </br> 5.0.0-1012-azure에서 5.0.0-1018-azure|

### <a name="debian-kernel-versions"></a>Debian 커널 버전


**지원되는 릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
Debian 7 | [9.29,][9.29 UR] [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR]| 3.2.0-4-amd64에서 3.2.0-6-amd64까지, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR] | 3.16.0-4-amd64 ~ 3.16.0-10 amd64, 4.9.0-0.bpo.4-amd64 ~ 4.9.0-0.bpo.11-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 ~ 3.16.0-10 amd64, 4.9.0-0.bpo.4-amd64 ~ 4.9.0-0.bpo.9-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 지원되는 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | [9.28][9.28 UR] | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-기본값 3.12.74-60.64.118-기본값</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-기본값 4.4.121-92.117-기본값</br></br>SP3 4.4.73-5-기본값 4.4.180-94.100-기본값</br></br>SP3 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-기본값 4.12.14-95.29-기본값</br>SP4 4.12.14-6.3-azure에서 4.12.14-6.23-azure |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-기본값 3.12.74-60.64.115-기본값</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-기본값 4.4.121-92.114-기본값</br></br>SP3 4.4.73-5-기본값 4.4.180-94.97-기본값</br></br>SP3 4.4.138-4.7-azure에서 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-기본값 4.12.14-95.19-기본값</br>SP4 4.12.14-6.3-azure에서 4.12.14-6.15-azure |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-기본값 3.12.74-60.64.110-기본값</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-기본값 4.4.121-92.109-기본값</br></br>SP3 4.4.73-5-기본값 4.4.178-94.91-기본값</br></br>SP3 4.4.138-4.7-azure에서 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-기본값 4.12.14-95.16-기본값</br>SP4 4.12.14-6.3-azure에서 4.12.14-6.9-azure |
SUSE 리눅스 엔터프라이즈 서버 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default에서 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default에서 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default에서 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-기본값 4.4.121-92.104-기본값</br></br>SP3 4.4.73-5-기본값 4.4.176-94.88-기본값</br></br>SP3 4.4.138-4.7-azure에서 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-기본값 4.12.14-95.13-기본값</br>SP4 4.12.14-6.3-azure에서 4.12.14-6.9-azure |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE 리눅스 엔터프라이즈 서버 15 지원 커널 버전

**릴리스** | **모바일 서비스 버전** | **커널 버전** |
--- | --- | --- |
수지 리눅스 엔터프라이즈 서버 15 및 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | 모든 [재고 SUSE 15 및 15 커널이 지원됩니다.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15) </br></br> 4.12.14-5.5-azure에서 4.12.14-8.22-azure

## <a name="linux-file-systemsguest-storage"></a>Linux 파일 시스템/게스트 스토리지

**구성 요소** | **지원됨**
--- | ---
파일 시스템 | 내항3, 내근, XFS, BTRFS(이 표에 따라 적용되는 조건)
볼륨 관리자 | - LVM이 지원됩니다.<br/> - LVM의 /boot는 [업데이트 롤업 31(모빌리티](https://support.microsoft.com/help/4478871/) 서비스의 버전 9.20)에서 지원됩니다. 이전 모빌리티 서비스 버전에서는 지원되지 않습니다.<br/> - 여러 OS 디스크가 지원되지 않습니다.
반가상화 스토리지 디바이스 | 반가상화 드라이버에서 내보낸 디바이스는 지원되지 않습니다.
다중 큐 블록 IO 디바이스 | 지원되지 않습니다.
HP CCISS 스토리지 컨트롤러가 있는 물리적 서버 | 지원되지 않습니다.
디바이스/탑재 지점 명명 규칙 | 디바이스 이름과 탑재 지점 이름은 고유해야 합니다.<br/> 두 장치/마운트 지점에 대/소문자를 구분하는 이름이 없는지 확인합니다. 예를 들어 장치1 및 *Device1과* 동일한 *Device1* VM에 대한 장치의 이름은 지원되지 않습니다.
디렉터리 | 버전 9.20(업데이트 [롤업 31에서](https://support.microsoft.com/help/4478871/)릴리스됨) 이전에 모빌리티 서비스의 버전을 실행 중인 경우 다음 제한 사항이 적용됩니다.<br/><br/> - 이러한 디렉토리 (별도의 파티션 / 파일 시스템으로 설정하는 경우) 소스 서버에서 동일한 OS 디스크에 있어야합니다 : /(루트), / 부팅, /usr, /usr/ local, /var, /etc.</br> - /boot 디렉터리디스크 파티션에 있어야 하며 LVM 볼륨이 아니어야 합니다.<br/><br/> 버전 9.20 이후부터는 이러한 제한이 적용되지 않습니다. 
루트 디렉터리 | - 부팅 디스크는 GPT 파티션 형식에 없어야합니다. 이는 Azure 아키텍처 제한 사항입니다. GPT 디스크는 데이터 디스크로 지원됩니다.<br/><br/> VM의 여러 부팅 디스크가 지원되지 않음<br/><br/> - 하나 이상의 디스크에서 LVM 볼륨에 /boot가 지원되지 않습니다.<br/> - 부팅 디스크가 없는 컴퓨터는 복제할 수 없습니다.
사용 가능한 공간 요구 사항| /root 파티션: 2GB <br/><br/> 설치 폴더: 250MB
XFSv5 | 메타데이터 체크섬과 같은 XFS 파일 시스템의 XFSv5 기능이 지원됩니다(모빌리티 서비스 버전 9.10 이후).<br/> xfs_info 유틸리티를 사용하여 파티션에 대한 XFS 수퍼 블록을 확인합니다. 1로 설정된 경우 `ftype` XFSv5 기능이 사용 중입니다.
Btrfs | BTRFS는 [업데이트 롤업 34(모빌리티](https://support.microsoft.com/help/4490016) 서비스의 버전 9.22 버전)부터 지원됩니다. 다음과 같은 경우 BTRFS가 지원되지 않습니다.<br/><br/> - 방한을 활성화한 후 BTRFS 파일 시스템 하위 볼륨이 변경됩니다.</br> - BTRFS 파일 시스템은 여러 디스크에 분산되어 있습니다.</br> - BTRFS 파일 시스템은 RAID를 지원합니다.

## <a name="vmdisk-management"></a>VM/디스크 관리

**작업** | **세부 정보**
--- | ---
복제된 VM에서 디스크 크기 조정 | 장애 조치 전에 VM 원본에서 직접 VM 속성에서 지원됩니다. 복제를 사용하지 않도록 설정/다시 사용할 필요가 없습니다.<br/><br/> 장애 조치 후 소스 VM을 변경하면 변경 내용이 캡처되지 않습니다.<br/><br/> 장애 조치 후 Azure VM의 디스크 크기를 변경하는 경우 장애 조치(failover)를 다시 하면 사이트 복구가 업데이트와 함께 새 VM을 만듭니다.
복제된 VM에 디스크 추가 | 지원되지 않습니다.<br/> VM에 대한 복제를 사용하지 않도록 설정하고 디스크를 추가한 다음 복제를 다시 활성화합니다.

## <a name="network"></a>네트워크

**구성 요소** | **지원됨**
--- | ---
호스트 네트워크 NIC 팀 | VMware VM에서 지원됩니다. <br/><br/>물리적 컴퓨터 복제에 지원되지 않습니다.
호스트 네트워크 VLAN | 예.
호스트 네트워크 IPv4 | 예.
호스트 네트워크 IPv6 | 아니요.
게스트/서버 네트워크 NIC 팀 | 아니요.
게스트/서버 네트워크 IPv4 | 예.
게스트/서버 네트워크 IPv6 | 아니요.
게스트/서버 네트워크 정적 IP(Windows) | 예.
게스트/서버 네트워크 정적 IP(Linux) | 예. <br/><br/>VM이 장애 복구(Failback) 시 DHCP를 사용하도록 구성되어 있습니다.
게스트/서버 네트워크 다중 NIC | 예.


## <a name="azure-vm-network-after-failover"></a>Azure VM 네트워크(장애 조치(failover) 후)

**구성 요소** | **지원됨**
--- | ---
Azure ExpressRoute | 예
ILB | 예
ELB | 예
Azure Traffic Manager | 예
다중 NIC | 예
예약된 IP 주소 | 예
IPv4 | 예
원본 IP 주소 유지 | 예
Azure 가상 네트워크 서비스 엔드포인트<br/> | 예
가속된 네트워킹 | 예

## <a name="storage"></a>스토리지
**구성 요소** | **지원됨**
--- | ---
동적 디스크 | OS 디스크는 기본 디스크여야 합니다. <br/><br/>데이터 디스크는 동적 디스크일 수 있습니다.
Docker 디스크 구성 | 예
호스트 NFS | VMware의 경우 예<br/><br/> 물리적 서버의 경우 아니요
호스트 SAN(iSCSI/FC) | 예
호스트 vSAN | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음
호스트 다중 경로(MPIO) | 예. 테스트 제품: Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM for CLARiiON
호스트 가상 볼륨(VVol) | VMware의 경우 예<br/><br/> 물리적 서버의 경우 해당 없음
게스트/서버 VMDK | 예
게스트/서버 공유 클러스터 디스크 | 예
게스트/서버 암호화된 디스크 | 예
게스트/서버 NFS | 예
게스트/서버 iSCSI | 마이그레이션의 경우 - 예<br/>재해 복구의 경우 - 아니요, iSCSI는 VM에 연결된 디스크로 장애 복구됩니다.
게스트/서버 SMB 3.0 | 예
게스트/서버 RDM | 예<br/><br/> 물리적 서버의 경우 해당 없음
게스트/서버 디스크 > 1 TB | 예. 디스크는 1024MB보다 커야 합니다.<br/><br/>관리 디스크로 복제할 때 최대 8,192GB(9.26 버전 이후)<br></br> 저장소 계정으로 복제할 때 최대 4,095GB
4K 논리적 및 4k 물리적 섹터 크기 포함 게스트/서버 디스크 | 예
4K 논리 및 512바이트 물리적 섹터 크기의 게스트/서버 디스크 | 예
스트라이프 디스크 포함 게스트/서버 볼륨 4TB 이상 <br/><br/>논리 볼륨 관리(LVM)| 예
게스트/서버 - 스토리지 공간 | 예
게스트/서버 디스크 핫 추가/제거 | 예
게스트/서버 - 디스크 제외 | 예
게스트/서버 다중 경로(MPIO) | 예
게스트/서버 GPT 파티션 | [업데이트 롤업 37(모빌리티](https://support.microsoft.com/help/4508614/) 서비스의 버전 9.25)에서 5개의 파티션이 지원됩니다. 이전에는 4개가 지원되었습니다.
ReFS | 탄력적 파일 시스템은 모빌리티 서비스 버전 9.23 이상으로 지원됩니다.
게스트/서버 EFI/UEFI 부팅 | - 윈도우 서버 2012 이상, SLES 12 SP4 및 RHEL 8.0 이동성 에이전트 버전 9.30 이후 지원<br/> - 보안 UEFI 부팅 유형이 지원되지 않습니다.

## <a name="replication-channels"></a>복제 채널

|**복제 유형**   |**지원됨**  |
|---------|---------|
|오프로드된 데이터 전송(ODX)    |       예  |
|오프라인 시드        |   예      |
| Azure Data Box | 예

## <a name="azure-storage"></a>Azure Storage

**구성 요소** | **지원됨**
--- | ---
로컬 중복 스토리지 | 예
지역 중복 스토리지 | 예
읽기 액세스 지역 중복 스토리지 | 예
쿨 스토리지 | 예
핫 스토리지| 예
블록 Blob | 예
미사용 암호화(SSE)| 예
미사용 암호화(CMK)| 예(PowerShell Az 3.3.0 모듈 을 통해 이후)
Premium Storage | 예
Import/Export 서비스 | 예
VNet용 Azure 저장소 방화벽 | 예.<br/> 대상 저장소/캐시 저장소 계정(복제 데이터를 저장하는 데 사용)에 대해 구성됩니다.
범용 v2 저장소 계정(핫 및 쿨 계층) | 예(V1에 비해 V2의 거래 비용이 상당히 높습니다).

## <a name="azure-compute"></a>Azure Compute

**기능** | **지원됨**
--- | ---
가용성 집합 | 예
가용성 영역 | 예
HUB | 예
관리 디스크 | 예

## <a name="azure-vm-requirements"></a>Azure VM 요구 사항

Azure에 복제된 온-프레미스 VM은 이 테이블에 요약된 Azure VM 요구 사항을 충족해야 합니다. 사이트 복구가 복제에 대한 필수 구성 조건 검사를 실행하면 일부 요구 사항이 충족되지 않으면 검사가 실패합니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
게스트 운영 체제 | 복제된 컴퓨터에 대해 [지원되는 운영 체제](#replicated-machines)를 확인합니다. | 지원되지 않는 경우 확인이 실패합니다.
게스트 운영 체제 아키텍처 | 64비트. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 크기 | 최대 2,048GB. | 지원되지 않는 경우 확인이 실패합니다.
운영 체제 디스크 수 | 1 | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 수 | 64개 이하. | 지원되지 않는 경우 확인이 실패합니다.
데이터 디스크 크기 | 관리 디스크로 복제할 때 최대 8,192GB(9.26 버전 이후)<br></br>저장소 계정으로 복제할 때 최대 4,095GB| 지원되지 않는 경우 확인이 실패합니다.
네트워크 어댑터 | 여러 어댑터가 지원됩니다. |
공유 VHD | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
FC 디스크 | 지원되지 않습니다. | 지원되지 않는 경우 확인이 실패합니다.
BitLocker | 지원되지 않습니다. | 컴퓨터의 복제를 사용하도록 설정하기 전에 Bitlocker를 사용하지 않도록 설정해야 합니다. |
VM 이름 | 1~63자 사이입니다.<br/><br/> 문자, 숫자 및 하이픈으로 제한됩니다.<br/><br/> 컴퓨터 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |  Site Recovery에서 컴퓨터 속성의 값을 업데이트합니다.

## <a name="resource-group-limits"></a>리소스 그룹 제한

단일 리소스 그룹에서 보호할 수 있는 가상 시스템의 수를 이해하려면 [구독 제한 및 할당량에](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)대한 문서를 참조하십시오.

## <a name="churn-limits"></a>이탈 제한

다음 테이블에는 Azure Site Recovery 제한이 제공됩니다.
- 이러한 제한은 테스트를 기반으로 하지만 가능한 모든 앱 I/O 조합을 다루지는 않습니다.
- 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.
- 최상의 결과를 얻으려면 [배포 플래너 도구를](site-recovery-deployment-planner.md)실행하고 테스트 장애 조치(failover)를 사용하여 광범위한 응용 프로그램 테스트를 수행하여 앱에 대한 실제 성능 그림을 얻는 것이 좋습니다.

**복제 대상** | **평균 원본 디스크 I/O 크기** |**평균 원본 디스크 데이터 변동** | **일일 총 원본 디스크 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20 MB/s | 디스크당 1684GB


**원본 데이터 변동률** | **최대 제한**
---|---
VM의 모든 디스크에 대한 최고 데이터 변동률 | 54MB/초
프로세스 서버에서 지원하는 1일 최대 데이터 변동률 | 2TB

- 여기서는 I/O가 30% 겹치고 있다고 가정하는 평균 숫자입니다.
- Site Recovery는 중첩 비율, 더 큰 쓰기 크기 및 실제 워크로드 I/O 동작에 따라 더 높은 처리량을 다룰 수 있습니다.
- 이 숫자는 약 5분의 일반적인 백로그를 가정합니다. 즉, 데이터를 업로드한 후에 처리되며 5분 내에 복구 지점이 생성됩니다.

## <a name="vault-tasks"></a>자격 증명 모음 작업

**작업** | **지원됨**
--- | ---
리소스 그룹 간 자격 증명 모음 이동 | 예
구독 내 및 구독 간에 볼트 이동 | 예
스토리지 그룹 간 스토리지, 네트워크, Azure VM 이동 | 예
구독 내 및 구독 간에 저장소, 네트워크, Azure VM을 이동합니다. | 예


## <a name="obtain-latest-components"></a>최신 부품 구하기

**이름** | **설명** | **세부 정보**
--- | --- | ---
구성 서버 | 온-프레미스에 설치되었습니다.<br/> 온-프레미스 VMware 서버 또는 물리적 컴퓨터와 Azure 간의 통신을 조정합니다. | - 구성 서버에 [대해 자세히 알아보세요.](vmware-physical-azure-config-process-server-overview.md)<br/> - 최신 버전으로 업그레이드하는 [방법에 대해 알아봅니다.](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)<br/> - 구성 서버 [설정에 대해 자세히 알아보세요.](vmware-azure-deploy-configuration-server.md)
프로세스 서버 | 기본적으로 구성 서버에 설치합니다.<br/> 복제 데이터를 수신하고 캐싱, 압축 및 암호화로 최적화한 다음 Azure로 보냅니다.<br/> 배포가 증가함에 따라 더 많은 양의 복제 트래픽을 처리하기 위해 프로세스 서버를 추가할 수 있습니다. | - 프로세스 서버에 [대해 자세히 알아보세요.](vmware-physical-azure-config-process-server-overview.md)<br/> - 최신 버전으로 업그레이드하는 [방법에 대해 알아봅니다.](vmware-azure-manage-process-server.md#upgrade-a-process-server)<br/> - 확장 프로세스 서버 [설정에 대해 자세히 알아봅니다.](vmware-physical-large-deployment.md#set-up-a-process-server)
Mobility Service | 복제하려는 VM웨어 VM 또는 실제 서버에 설치됩니다.<br/> 온-프레미스 VMware 서버/물리적 서버와 Azure 간의 복제를 조정합니다.| - 모빌리티 서비스에 [대해 자세히 알아보십시오.](vmware-physical-mobility-service-overview.md)<br/> - 최신 버전으로 업그레이드하는 [방법에 대해 알아봅니다.](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)<br/>



## <a name="next-steps"></a>다음 단계
VMware VM의 재해 복구용으로 Azure를 준비하는 방법을 [알아봅니다](tutorial-prepare-azure.md).

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
