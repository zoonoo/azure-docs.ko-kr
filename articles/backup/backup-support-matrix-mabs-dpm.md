---
title: MABS 및 System Center DPM 지원 매트릭스
description: 이 문서에서는 MABS(Microsoft Azure Backup Server) 및 System Center DPM을 사용하여 온-프레미스 및 Azure VM 리소스를 백업할 때의 Azure Backup 지원을 요약해서 설명합니다.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102609756"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure Backup Server 또는 System Center DPM을 사용하는 백업에 대한 지원 매트릭스

[Azure Backup 서비스](backup-overview.md)를 사용하여 온-프레미스 머신 및 워크로드, Azure VM(가상 머신)을 백업할 수 있습니다. 이 문서에서는 MABS(Microsoft Azure Backup Server) 또는 System Center DPM(Data Protection Manager)과 Azure Backup을 사용하여 컴퓨터를 백업할 때의 지원 설정 및 제한 사항을 요약해서 설명합니다.

## <a name="about-dpmmabs"></a>DPM/MABS 정보

[System Center DPM](/system-center/dpm/dpm-overview)은 엔터프라이즈 컴퓨터 및 데이터의 백업 및 복구를 구성하고 용이하게 하고 관리하는 엔터프라이즈 솔루션입니다. 이 기능은 [System Center](https://www.microsoft.com/system-center/pricing) 제품군에 속해 있습니다.

MABS는 온-프레미스 물리적 서버, VM과 여기에서 실행되는 앱을 백업하는 데 사용할 수 있는 서버 제품입니다.

MABS는 System Center DPM을 기반으로 하고 유사한 기능을 제공하나 다음과 같이 몇 가지 차이점이 있습니다.

- MABS를 실행하기 위해 System Center 라이선스가 필요하지 않습니다.
- DPM 및 MABS 모두 Azure에서 장기 백업 스토리지를 제공합니다. 또한 DPM을 사용하면 테이프의 장기 보관할 수 있게 데이터를 백업할 수 있습니다. MABS는 이 기능을 제공하지 않습니다.
- [보조 DPM 서버를 사용하여 주 DPM 서버를 백업할 수 있습니다](/system-center/dpm/back-up-the-dpm-server). 보조 서버는 주 서버의 데이터베이스와 주 서버에 저장된 데이터 원본 복제본을 보호합니다. 주 서버에서 오류가 발생하는 경우 보조 서버는 주 서버를 다시 사용할 수 있을 때까지 주 서버에 의해 보호되는 작업을 계속 보호할 수 있습니다.  MABS는 이 기능을 제공하지 않습니다.

[Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57520)에서 MABS를 다운로드할 수 있습니다. 온-프레미스 또는 Azure VM에서 실행할 수 있습니다.

DPM 및 MABS는 다양한 앱, 서버 및 클라이언트 운영 체제의 백업을 지원합니다. 다음과 같은 여러 백업 시나리오를 제공합니다.

- 시스템 상태 백업 또는 완전 백업을 사용하여 컴퓨터 수준에서 백업할 수 있습니다.
- 특정 볼륨, 공유, 폴더 및 파일을 백업할 수 있습니다.
- 최적화된 앱 인식 설정을 사용하여 특정 앱을 백업할 수 있습니다.

## <a name="dpmmabs-backup"></a>DPM/MABS 백업

DPM/MABS 및 Azure Backup을 사용하는 백업은 다음과 같이 작동합니다.

1. 백업할 각 시스템에 DPM/MABS 보호 에이전트가 설치됩니다.
1. 컴퓨터 및 앱이 DPM/MABS의 로컬 스토리지에 백업됩니다.
1. MARS(Microsoft Azure Recovery Services) 에이전트가 DPM 서버/MABS에 설치됩니다.
1. MARS 에이전트는 Azure Backup을 사용하여 Azure의 백업 Recovery Services 자격 증명 모음으로 DPM/MABS 디스크를 백업합니다.

자세한 내용은 다음을 참조하세요.

- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대한 [지원되는 기능을 검토](backup-support-matrix-mars-agent.md)합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

**시나리오** | **에이전트** | **위치**
--- | --- | ---
**온-프레미스 머신/워크로드 백업**: | DPM/MABS 보호 에이전트가 백업하려는 머신에서 실행됩니다.<br/><br/> DPM/MABS 서버의 MARS 에이전트<br/> 이 기능을 사용하도록 설정하는 데 필요한 Microsoft Azure Recovery Services 에이전트 또는 Azure Backup 에이전트의 최소 버전은 2.0.8719.0입니다.  | DPM/MABS가 온-프레미스에서 실행되어야 합니다.

## <a name="supported-deployments"></a>지원되는 배포

DPM/MABS는 다음 표에 요약된 것처럼 배포할 수 있습니다.

**배포** | **지원** | **세부 정보**
--- | --- | ---
**온-프레미스에 배포** | 물리적 서버이지만 실제 클러스터에는 없습니다.<br/><br/>Hyper-V VM. 독립 실행형 하이퍼바이저 또는 클러스터에서 MABS를 게스트 컴퓨터로 배포할 수 있습니다. 클러스터 또는 독립 실행형 하이퍼바이저의 노드에는 배포할 수 없습니다. Azure Backup Server는 단일 용도의 전용 서버에서 실행하도록 설계되었습니다.<br/><br/> VMware 환경에서 Windows 가상 머신으로. | 온-프레미스 MABS 서버는 Azure 기반 워크로드를 보호할 수 없습니다. <br><br> 자세한 내용은 [보호 매트릭스](backup-mabs-protection-matrix.md)를 참조하세요.
**Azure Stack VM으로 배포** | MABS만 | DPM은 Azure Stack VM을 백업하는 데 사용할 수 없습니다.
**Azure VM으로 배포** | Azure VM과 해당 VM에서 실행 중인 워크로드를 보호합니다. | Azure에서 실행되는 DPM/MABS는 온-프레미스 컴퓨터를 백업할 수 없습니다. Azure IaaS VM에서 실행 중인 워크로드만 보호할 수 있습니다.

## <a name="supported-mabs-and-dpm-operating-systems"></a>지원되는 MABS 및 DPM 운영 체제

Azure Backup은 다음 운영 체제 중 하나에서 실행 중인 DPM/MABS를 백업할 수 있습니다. 운영 체제는 최신 서비스 팩 및 업데이트를 실행하고 있어야 합니다.

**시나리오** | **DPM/MABS**
--- | ---
**Azure VM의 MABS** |  Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> 마켓플레이스의 이미지로 시작하는 것이 좋습니다.<br/><br/> 4코어 및 8GB RAM이 있는 최소 Standard_A4_v2
**Azure VM의 DPM** | System Center 2012 R2 업데이트 3 이상<br/><br/> [System Center에 필요한](/system-center/dpm/prepare-environment-for-dpm#dpm-server) Windows 운영 체제<br/><br/> 마켓플레이스의 이미지로 시작하는 것이 좋습니다.<br/><br/> 4코어 및 8GB RAM이 있는 최소 Standard_A4_v2
**MABS 온-프레미스** |  MABS v3 이상: Windows Server 2016 또는 Windows Server 2019
**DPM 온-프레미스** | 물리적 서버/Hyper-V VM: System Center 2012 SP1 이상<br/><br/> VMware VM: System Center 2012 R2 업데이트 5 이상

>[!NOTE]
>Windows Server Core 또는 Microsoft Hyper-V Server에서는 Azure Backup Server 설치가 지원되지 않습니다.

## <a name="management-support"></a>관리 지원

**문제점** | **세부 정보**
--- | ---
**설치** | 단일 용도의 컴퓨터에 DPM/MABS를 설치합니다.<br/><br/> 도메인 컨트롤러, 애플리케이션 서버 역할이 설치된 컴퓨터, Microsoft Exchange Server 또는 System Center Operations Manager를 실행하는 컴퓨터 또는 클러스터 노드에는 DPM/MABS를 설치하지 마세요.<br/><br/> [모든 DPM 시스템 요구 사항을 검토합니다](/system-center/dpm/prepare-environment-for-dpm#dpm-server).
**도메인** | DPM/MABS는 도메인에 가입되어 있어야 합니다. DPM/MABS를 먼저 설치한 후 도메인에 가입합니다. 배포가 지원되지 않으면 DPM/MABS를 새 도메인으로 이동
**스토리지** | MBS(최신 백업 스토리지)는 DPM 2016/MABS v2 이상에서 지원됩니다. MABS v1에는 사용할 수 없습니다.
**MABS 업그레이드** | MABS v3를 직접 설치하거나 MABS v2에서 MABS v3로 업그레이드할 수 있습니다. [자세히 알아보기](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS 이동** | MBS를 사용하는 경우 스토리지 보존이 지원될 때 MABS를 새 서버로 이동<br/><br/> 새 서버는 원래 서버와 이름이 같아야 합니다. 동일한 스토리지 풀을 유지하고 동일한 MABS 데이터베이스를 사용하여 데이터 복구 지점을 저장하려는 경우에는 이름을 변경할 수 없습니다.<br/><br/> MABS 데이터베이스를 복원해야 하므로 백업이 필요합니다.

>[!NOTE]
>DPM/MABS 서버의 이름 변경은 지원되지 않습니다.

## <a name="mabs-support-on-azure-stack"></a>Azure Stack의 MABS 지원

단일 위치에서 Azure Stack VM 및 워크로드 백업을 관리할 수 있도록 Azure Stack VM에 MABS를 배포할 수 있습니다.

**구성 요소** | **세부 정보**
--- | ---
**Azure Stack VM의 MABS** | 최소 크기 A2. Azure Marketplace의 Windows Server 2012 R2 또는 Windows Server 2016 이미지로 시작하는 것이 좋습니다.<br/><br/> MABS VM에 다른 항목은 설치하지 않습니다.
**MABS 스토리지** | MABS VM에 대해 별도의 스토리지 계정을 사용합니다. MABS에서 실행되는 MARS 에이전트는 캐시 위치로 사용되고 클라우드에서 복원된 데이터를 저장할 임시 스토리지가 필요합니다.
**MABS 스토리지 풀** | MABS 스토리지 풀의 크기는 MABS VM에 연결된 디스크의 수와 크기에 따라 결정됩니다. 각 Azure Stack VM 크기는 최대 디스크 수를 갖습니다. 예를 들어 A2는 4개의 디스크입니다.
**MABS 보존** | 백업한 데이터를 5일 넘게 로컬 MABS 디스크에 보존하지 않습니다.
**MABS 강화** | 배포를 강화하기 위해 MABS VM의 크기를 늘릴 수 있습니다. 예를 들어, A에서 D 시리즈로 변경할 수 있습니다.<br/><br/> 정기적으로 Azure에 백업하여 데이터를 오프로드할 수도 있습니다. 필요한 경우 추가 MABS 서버를 배포할 수 있습니다.
**MABS의 .NET Framework** | MABS VM에는 .NET Framework 3.3 SP1 이상을 설치해야 합니다.
**MABS 도메인** | MABS VM은 도메인에 가입되어 있어야 합니다. 관리자 권한이 있는 도메인 사용자는 VM에 MABS를 설치해야 합니다.
**Azure Stack VM 데이터 백업** | 파일, 폴더 및 앱을 백업할 수 있습니다.
**지원되는 백업** | 백업하려는 VM에서 다음 운영 체제가 지원됩니다.<br/><br/> Windows Server 반기 채널(Datacenter, Enterprise, Standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**Azure Stack VM에 대한 SQL Server 지원** | SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1을 백업합니다.<br/><br/> 데이터베이스를 백업 및 복구합니다.
**Azure Stack VM에 대한 SharePoint 지원** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> 팜, 데이터베이스, 프런트 엔드 및 웹 서버를 백업 및 복구합니다.
**백업한 VM에 대한 네트워크 요구 사항** | Azure Stack 워크로드의 모든 VM은 동일한 가상 네트워크와 동일한 구독에 속해야 합니다.

## <a name="dpmmabs-networking-support"></a>DPM/MABS 네트워킹 지원

### <a name="url-access"></a>URL 액세스

DPM 서버/MABS 서버는 다음 URL 및 IP 주소에 액세스할 수 있어야 합니다.

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP 주소
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Azure ExpressRoute 지원

공용 피어링(이전 회로에 사용 가능) 및 Microsoft 피어링을 사용하여 Azure ExpressRoute를 통해 데이터를 백업할 수 있습니다. 개인 피어링에 대한 백업은 지원되지 않습니다.

공용 피어링 사용: 다음 도메인/주소에 대한 액세스를 확인합니다.

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP 주소
  * 20.190.128.0/18
  * 40.126.0.0/18

Microsoft 피어링을 사용하여 다음 서비스/지역 및 관련 커뮤니티 값을 선택합니다.

- Azure Active Directory(12076:5060)
- Microsoft Azure 지역(Recovery Services 자격 증명 모음의 위치에 따름)
- Azure Storage(Recovery Services 자격 증명 모음의 위치에 따름)

자세한 내용은 [ExpressRoute 라우팅 요구 사항](../expressroute/expressroute-routing.md)을 참조하세요.

>[!NOTE]
>공용 피어링은 새 회로에 사용되지 않습니다.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup에 대한 DPM/MABS 연결

백업이 제대로 작동하고 Azure 구독이 활성화되려면 Azure Backup 서비스에 연결되어야 합니다. 다음 표에서는 이러한 두 가지 조건이 충족되지 않을 경우에 발생하는 동작을 보여 줍니다.

**MABS-Azure 연결** | **구독** | **백업/복원**
--- | --- | ---
연결됨 | Active | DPM/MABS 디스크에 백업합니다.<br/><br/> Azure에 백업합니다.<br/><br/> 디스크에서 복원합니다.<br/><br/> Azure에서 복원합니다.
연결됨 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료된 경우 디스크나 Azure에서 복원할 수 있습니다.<br/><br/> 구독이 서비스 해제된 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.
15일 넘게 연결되지 않음 | Active | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 디스크 또는 Azure에서 복원할 수 있습니다.
15일 넘게 연결되지 않음 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료된 경우 디스크나 Azure에서 복원할 수 있습니다.<br/><br/> 구독이 서비스 해제된 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.

## <a name="domain-and-domain-trusts-support"></a>도메인 및 도메인 트러스트 지원

|요구 사항 |세부 정보 |
|---------|---------|
|도메인    | DPM/MABS 서버는 Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 도메인에 있어야 합니다.        |
|도메인 신뢰   |  DPM/MABS는 개별 포리스트 간에 포리스트 수준의 양방향 신뢰를 설정하는 동안에는 포리스트 간 데이터 보호를 지원합니다.   <BR><BR>   DPM/MABS는 DPM/MABS 서버 도메인과 양방향 신뢰 관계가 있는 포리스트 내에서 도메인 간 서버와 워크스테이션을 보호할 수 있습니다. 작업 그룹 또는 신뢰할 수 없는 도메인에 있는 컴퓨터를 보호하려면 [작업 그룹 및 신뢰할 수 없는 도메인의 워크로드 백업 및 복원](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains)을 참조하세요. <br><br> Hyper-V 서버 클러스터를 백업하려면 MABS 서버와 동일한 도메인에 있거나 신뢰할 수 있는 도메인 또는 하위 도메인에 있어야 합니다. 신뢰할 수 없는 도메인에 있는 서버 및 클러스터 또는 작업은 단일 서버의 경우 NTLM 또는 인증서 인증, 클러스터의 경우 인증서 인증을 사용하여 백업할 수 있습니다.  |

## <a name="dpmmabs-storage-support"></a>DPM/MABS 스토리지 지원

DPM/MABS에 백업된 데이터는 로컬 디스크 스토리지에 저장됩니다.

USB 또는 이동식 드라이브는 지원되지 않습니다.

DPM/MABS 볼륨에서는 NTFS 압축이 지원되지 않습니다.

디스크를 스토리지 풀에 추가한 후에만 BitLocker를 사용하도록 설정할 수 있습니다. BitLocker를 추가하기 전에 BitLocker를 사용하도록 설정하지 않습니다.

NAS(네트워크 연결 스토리지)는 DPM 스토리지 풀에서 지원되지 않습니다.

**스토리지** | **세부 정보**
--- | ---
**MBS** | MBS(최신 백업 스토리지)는 DPM 2016/MABS v2 이상에서 지원됩니다. MABS v1에는 사용할 수 없습니다.
**Azure VM의 MABS 스토리지** | 데이터는 DPM/MABS VM에 연결되고 DPM/MABS에서 관리되는 Azure 디스크에 저장됩니다. DPM/MABS 스토리지 풀에 사용할 수 있는 디스크 수는 VM 크기에 따라 제한됩니다.<br/><br/> A2 VM: 4개 디스크, A3 VM: 8개 디스크, A4 VM: 16개 디스크(각 디스크당 최대 1TB 크기). 이에 따라 사용 가능한 총 백업 스토리지 풀이 결정됩니다.<br/><br/> 백업할 수 있는 데이터 양은 연결된 디스크의 수와 크기에 따라 달라집니다.
**Azure VM의 MABS 데이터 보존** | 하루 동안 DPM/MABS Azure 디스크에 데이터를 유지하고 더 오래 보존하려는 경우 DPM/MABS에서 자격 증명 모음으로 백업하는 것이 좋습니다. 이런 식으로 대량의 데이터를 Azure Backup으로 오프로드하여 보호할 수 있습니다.

### <a name="modern-backup-storage-mbs"></a>MBS(최신 백업 스토리지)

DPM 2016/MABS v2(Windows Server 2016에서 실행) 이상에서 MBS(최신 백업 스토리지)를 활용할 수 있습니다.

- MBS 백업은 ReFS(Resilient File System) 디스크에 저장됩니다.
- MBS는 더 빠른 백업과 보다 효율적인 스토리지 공간 사용을 위해 ReFS 블록 복제를 사용합니다.
- 로컬 DPM/MABS 스토리지 풀에 볼륨을 추가한 경우 드라이브 문자를 사용하여 구성했을 것입니다. 그런 후 여러 다른 볼륨에 워크로드 스토리지를 구성할 수 있습니다.
- DPM/MABS로 데이터를 백업하기 위해 보호 그룹을 만드는 경우 사용할 드라이브를 선택합니다. 예를 들어, SQL 또는 기타 높은 IOPS 워크로드의 백업은 고성능 드라이브에 저장하고, 덜 자주 백업하는 워크로드는 저성능 드라이브에 저장할 수 있습니다.

## <a name="supported-backups-to-mabs"></a>MABS에 대해 지원되는 백업

Azure Backup Server로 보호할 수 있는 다양한 서버 및 워크로드에 대한 자세한 내용은 [Azure Backup Server 보호 매트릭스](./backup-mabs-protection-matrix.md#protection-support-matrix)를 참조하세요.

## <a name="supported-backups-to-dpm"></a>DPM에 대해 지원되는 백업

Data Protection Manager를 사용하여 보호할 수 있는 다양한 서버 및 워크로드에 대한 자세한 내용은 [DPM이 백업할 수 있는 항목](/system-center/dpm/dpm-protection-matrix) 문서를 참조하세요.

- DPM/MABS로 백업한 클러스터형 워크로드는 DPM/MABS와 동일한 도메인이나 자식/신뢰할 수 있는 도메인에 있어야 합니다.
- 신뢰할 수 없는 도메인 또는 작업 그룹의 데이터를 백업하려면 NTLM/인증서 인증을 사용할 수 있습니다.

## <a name="deduplicated-volumes-support"></a>중복 제거된 볼륨 지원

>[!NOTE]
> MABS에 대한 중복 제거 지원은 운영 체제 지원에 따라 다릅니다.

### <a name="for-ntfs-volumes"></a>NTFS 볼륨의 경우

| 보호된 서버의 운영 체제  | MABS 서버의 운영 체제  | MABS 버전  | 중복 제거 지원 |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| Windows Server 2019                       | Windows Server 2019                  | MABS v3            | Y                    |
| Windows  Server 2016                       | Windows Server 2019                  | MABS v3            | Y*                   |
| Windows Server 2012 R2                    | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2012                       | Windows Server 2019                  | MABS v3            | N                    |
| Windows Server 2019                       | Windows  Server 2016                  | MABS v3            | Y**                  |
| Windows  Server 2016                       | Windows  Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012 R2                    | Windows  Server 2016                  | MABS v3            | Y                    |
| Windows Server 2012                       | Windows  Server 2016                  | MABS v3            | Y                    |

- \* WS 2019에서 실행되는 MABS v3을 사용하여 WS 2016 NTFS 중복 제거된 볼륨을 보호하는 경우 복구가 영향을 받을 수 있습니다. 복구를 중복 제거가 아닌 방식으로 수행하기 위한 픽스가 있습니다. MABS v3 UR1에서 이 픽스가 필요한 경우 MABS 고객 지원팀에 문의하세요.
- \** WS 2016에서 MABS v3을 사용하여 WS 2019 NTFS 중복 제거된 볼륨을 보호할 경우 백업 및 복원은 중복 제거되지 않습니다. 이는 MABS 서버에서 백업이 원본 NTFS 중복 제거된 볼륨보다 더 많은 공간을 차지함을 의미합니다.

**문제**: 보호된 서버 운영 체제를 Windows Server 2016에서 Windows Server 2019로 업그레이드하는 경우, 중복 제거 논리의 변경으로 인해 NTFS 중복 제거된 볼륨의 백업이 영향을 받습니다.

**해결 방법**: MABS v3 UR1에 대해 이 픽스가 필요한 경우 MABS 고객 지원팀에 문의하세요.

### <a name="for-refs-volumes"></a>ReFS 볼륨의 경우

>[!NOTE]
> 중복 제거된 ReFS 볼륨의 백업에 대한 몇 가지 문제를 파악했습니다. 이와 같은 문제를 해결하기 위해 노력하고 있으며 해결책이 마련되는 대로 이 섹션을 업데이트할 예정입니다. 그 때까지는 MABS v3에서 중복 제거된 ReFS 볼륨의 백업에 대한 지원이 제거됩니다.
>
> MABS v3 UR1 이상은 기본 ReFS 볼륨의 보호 및 복구를 계속 지원합니다.

## <a name="next-steps"></a>다음 단계

- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대한 지원되는 기능을 [검토](backup-support-matrix-mars-agent.md)합니다.
- MABS 서버를 [설정](backup-azure-microsoft-azure-backup.md)합니다.
- [DPM을 설정합니다](/system-center/dpm/install-dpm).
