---
title: MABS & 시스템 센터 DPM 지원 매트릭스
description: 이 문서에서는 Microsoft Azure 백업 서버(MABS) 또는 시스템 센터 DPM을 사용하여 온-프레미스 및 Azure VM 리소스를 백업할 때 Azure 백업 지원을 요약합니다.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6664f7b226b75b364fd1c83f2abc56b5a275eff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582656"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Microsoft Azure 백업 서버 또는 시스템 센터 DPM을 통한 백업 지원 매트릭스

Azure Backup [서비스를](backup-overview.md) 사용하여 온-프레미스 컴퓨터 및 워크로드 및 Azure 가상 시스템(VM)을 백업할 수 있습니다. 이 문서에서는 Microsoft Azure 백업 서버(MABS) 또는 시스템 센터 데이터 보호 관리자(DPM) 및 Azure 백업을 사용하여 컴퓨터를 백업하기 위한 지원 설정 및 제한 사항에 대해 요약합니다.

## <a name="about-dpmmabs"></a>DPM/MABS 소개

[System Center DPM은](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807) 엔터프라이즈 컴퓨터 및 데이터의 백업 및 복구를 구성, 용이하게, 관리하는 엔터프라이즈 솔루션입니다. 이 기능은 [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) 제품군에 속해 있습니다.

MABS는 온-프레미스 물리적 서버, VM 및 앱에서 실행되는 앱을 백업하는 데 사용할 수 있는 서버 제품입니다.

MABS는 시스템 센터 DPM을 기반으로 하며 다음과 같은 몇 가지 차이점과 유사한 기능을 제공합니다.

- MABS를 실행하기 위해 System Center 라이선스가 필요하지 않습니다.
- MABS 와 DPM 모두에 대해 Azure는 장기 백업 저장소를 제공합니다. 또한 DPM을 사용하면 테이프의 장기 보관할 수 있게 데이터를 백업할 수 있습니다. MABS는 이 기능을 제공하지 않습니다.
- 보조 DPM 서버를 통해 기본 DPM 서버를 백업할 수 있습니다. 보조 서버는 주 서버의 데이터베이스와 주 서버에 저장된 데이터 원본 복제본을 보호합니다. 주 서버에서 오류가 발생하는 경우 보조 서버는 주 서버를 다시 사용할 수 있을 때까지 주 서버에 의해 보호되는 작업을 계속 보호할 수 있습니다.  MABS는 이 기능을 제공하지 않습니다.

Microsoft 다운로드 센터에서 MABS를 [다운로드합니다.](https://www.microsoft.com/download/details.aspx?id=57520) 온-프레미스 또는 Azure VM에서 실행할 수 있습니다.

DPM 및 MABS는 다양한 앱, 서버 및 클라이언트 운영 체제의 백업을 지원합니다. 다음과 같은 여러 백업 시나리오를 제공합니다.

- 시스템 상태 백업 또는 완전 백업을 사용하여 컴퓨터 수준에서 백업할 수 있습니다.
- 특정 볼륨, 공유, 폴더 및 파일을 백업할 수 있습니다.
- 최적화된 앱 인식 설정을 사용하여 특정 앱을 백업할 수 있습니다.

## <a name="dpmmabs-backup"></a>DPM/MABS 백업

DPM/MABS 및 Azure 백업을 사용한 백업은 다음과 같이 작동합니다.

1. DPM/MABS 보호 에이전트는 백업될 각 컴퓨터에 설치됩니다.
1. 컴퓨터 및 앱은 DPM/MABS의 로컬 저장소에 백업됩니다.
1. MARS(Microsoft Azure Recovery Services) 에이전트가 DPM 서버/MABS에 설치됩니다.
1. MARS 에이전트는 Azure 백업을 사용하여 DPM/MABS 디스크를 Azure의 백업 복구 서비스 자격 증명 모음에 백업합니다.

자세한 내용은 다음을 참조하세요.

- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대해 [지원되는 내용을 검토합니다.](backup-support-matrix-mars-agent.md)

## <a name="supported-scenarios"></a>지원되는 시나리오

**시나리오** | **에이전트** | **위치**
--- | --- | ---
**온-프레미스 머신/워크로드 백업**: | DPM/MABS 보호 에이전트는 백업하려는 컴퓨터에서 실행됩니다.<br/><br/> DPM/MABS 서버의 MARS 에이전트입니다.<br/> 이 기능을 사용하도록 설정하는 데 필요한 Microsoft Azure Recovery Services 에이전트 또는 Azure Backup 에이전트의 최소 버전은 2.0.8719.0입니다.  | DPM/MABS는 온-프레미스에서 실행중이어야 합니다.

## <a name="supported-deployments"></a>지원되는 배포

DPM/MABS는 다음 표에 요약된 대로 배포할 수 있습니다.

**배포** | **지원** | **세부 정보**
--- | --- | ---
**온-프레미스에 배포** | 물리적 서버<br/><br/>Hyper-V VM<br/><br/> VMware VM | DPM/MABS가 VM웨어 VM으로 설치된 경우 해당 VM에서 실행 중인 VM웨어 VM 및 워크로드만 백업합니다.
**Azure Stack VM으로 배포** | MABS만 | DPM은 Azure Stack VM을 백업하는 데 사용할 수 없습니다.
**Azure VM으로 배포** | 해당 VM에서 실행 중인 Azure VM 및 워크로드 를 보호합니다. | Azure에서 실행되는 DPM/MABS는 온-프레미스 컴퓨터를 백업할 수 없습니다.

## <a name="supported-mabs-and-dpm-operating-systems"></a>지원되는 MABS 및 DPM 운영 체제

Azure Backup은 다음 운영 체제 중 어느 한 개라도 실행 중인 DPM/MABS 인스턴스를 백업할 수 있습니다. 운영 체제는 최신 서비스 팩 및 업데이트를 실행하고 있어야 합니다.

**시나리오** | **DPM/MABS**
--- | ---
**Azure VM의 MABS** |  윈도우 2016 데이터 센터.<br/><br/> 윈도우 2019 데이터 센터.<br/><br/> 마켓플레이스의 이미지로 시작하는 것이 좋습니다.<br/><br/> 최소 Standard_A4_v2 4개의 코어와 8GB RAM으로 사용됩니다.
**Azure VM의 DPM** | System Center 2012 R2 업데이트 3 이상<br/><br/> [System Center에 필요한](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) Windows 운영 체제<br/><br/> 마켓플레이스의 이미지로 시작하는 것이 좋습니다.<br/><br/> 최소 Standard_A4_v2 4개의 코어와 8GB RAM으로 사용됩니다.
**MABS 온-프레미스** |  MABS v3 이상: Windows 서버 2016 또는 Windows 서버 2019
**DPM 온-프레미스** | 물리적 서버/하이퍼 VVM: 시스템 센터 2012 SP1 이상.<br/><br/> VMware VM: 시스템 센터 2012 R2 업데이트 5 이상.

>[!NOTE]
>Azure 백업 서버 설치는 Windows 서버 코어 또는 Microsoft 하이퍼-V 서버에서 지원되지 않습니다.

## <a name="management-support"></a>관리 지원

**문제** | **세부 정보**
--- | ---
**설치** | 단일 용도의 컴퓨터에 DPM/MABS를 설치합니다.<br/><br/> 도메인 컨트롤러, 응용 프로그램 서버 역할 설치가 있는 컴퓨터, Microsoft Exchange Server 또는 시스템 센터 운영 관리자를 실행하는 컴퓨터 또는 클러스터 노드에 DPM/MABS를 설치하지 마십시오.<br/><br/> [모든 DPM 시스템 요구 사항을 검토합니다.](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)
**도메인** | DPM/MABS는 도메인에 조인되어야 합니다. DPM/MABS를 먼저 설치한 후 도메인에 가입합니다. 배포가 지원되지 않으면 DPM/MABS를 새 도메인으로 이동
**스토리지** | 최신 백업 저장소(MBS)는 DPM 2016/MABS v2 이상에서 지원됩니다. MABS v1에는 사용할 수 없습니다.
**MABS 업그레이드** | MABS v3를 직접 설치하거나 MABS v2에서 MABS v3로 업그레이드할 수 있습니다. [자세히 알아봅니다](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS 이동** | MBS를 사용하는 경우 스토리지 보존이 지원될 때 MABS를 새 서버로 이동<br/><br/> 새 서버는 원래 서버와 이름이 같아야 합니다. 동일한 스토리지 풀을 유지하고 동일한 MABS 데이터베이스를 사용하여 데이터 복구 지점을 저장하려는 경우에는 이름을 변경할 수 없습니다.<br/><br/> MABS 데이터베이스를 복원해야 하므로 백업이 필요합니다.

## <a name="mabs-support-on-azure-stack"></a>Azure Stack의 MABS 지원

단일 위치에서 Azure Stack VM 및 워크로드 백업을 관리할 수 있도록 Azure Stack VM에 MABS를 배포할 수 있습니다.

**구성 요소** | **세부 정보**
--- | ---
**Azure Stack VM의 MABS** | 적어도 크기 A2. Azure 마켓플레이스의 Windows 서버 2012 R2 또는 Windows Server 2016 이미지로 시작하는 것이 좋습니다.<br/><br/> MABS VM에 다른 것을 설치하지 마십시오.
**MABS 스토리지** | MABS VM에 대해 별도의 저장소 계정을 사용합니다. MABS에서 실행되는 MARS 에이전트는 캐시 위치에 대한 임시 저장소가 필요하며 클라우드에서 복원된 데이터를 보관해야 합니다.
**MABS 스토리지 풀** | MABS 저장소 풀의 크기는 MABS VM에 연결된 디스크의 수와 크기에 따라 결정됩니다. 각 Azure Stack VM 크기는 최대 디스크 수를 갖습니다. 예를 들어 A2는 4개의 디스크입니다.
**MABS 보존** | 로컬 MABS 디스크에 백업된 데이터를 5일 이상 보관하지 마십시오.
**MABS 강화** | 배포를 강화하기 위해 MABS VM의 크기를 늘릴 수 있습니다. 예를 들어 A에서 D 계열로 변경할 수 있습니다.<br/><br/> 또한 백업을 사용하여 데이터를 Azure에 정기적으로 오프로드하도록 할 수도 있습니다. 필요한 경우 추가 MABS 서버를 배포할 수 있습니다.
**MABS의 .NET Framework** | MABS VM에는 .NET 프레임워크 3.3 SP1 이상 설치가 필요합니다.
**MABS 도메인** | MABS VM은 도메인에 가입되어 있어야 합니다. 관리자 권한이 있는 도메인 사용자는 VM에 MABS를 설치해야 합니다.
**Azure Stack VM 데이터 백업** | 파일, 폴더 및 앱을 백업할 수 있습니다.
**지원되는 백업** | 이러한 운영 체제는 백업하려는 VM에 대해 지원됩니다.<br/><br/> 윈도우 서버 반기 채널(데이터 센터, 엔터프라이즈, 표준)<br/><br/> 윈도우 서버 2016, 윈도우 서버 2012 R2, 윈도우 서버 2008 R2
**Azure 스택 VM에 대 한 SQL 서버 지원** | SQL Server 2016, SQL Server 2014, SQL Server 2012 SP1을 백업합니다.<br/><br/> 데이터베이스를 백업하고 복구합니다.
**Azure Stack VM에 대한 SharePoint 지원** | SharePoint 2016, 쉐어포인트 2013, 쉐어포인트 2010.<br/><br/> 팜, 데이터베이스, 프런트 엔드 및 웹 서버를 백업하고 복구합니다.
**백업한 VM에 대한 네트워크 요구 사항** | Azure 스택 워크로드의 모든 VM은 동일한 가상 네트워크에 속해야 하며 동일한 구독에 속해야 합니다.

## <a name="dpmmabs-networking-support"></a>DPM/MABS 네트워킹 지원

### <a name="url-access"></a>URL 액세스

DPM 서버/MABS는 다음 URL에 액세스할 수 있어야 합니다.

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="azure-expressroute-support"></a>Azure 익스프레스루트 지원

공용 피어링(이전 회로에서 사용 가능) 및 Microsoft 피어링을 사용하여 Azure ExpressRoute를 통해 데이터를 백업할 수 있습니다. 개인 피어링을 통해 백업은 지원되지 않습니다.

공용 피어링: 다음 도메인/주소에 대한 액세스 확인:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Microsoft 피어링을 사용하면 다음 서비스/지역 및 관련 커뮤니티 값을 선택하십시오.

* Azure Active Directory (12076:5060)
* Microsoft Azure 지역(복구 서비스 볼트의 위치에 따라 다)
* Azure 저장소(복구 서비스 자격 증명 모음의 위치에 따라 다릅니다)

자세한 내용은 [ExpressRoute 라우팅 요구 사항을](https://docs.microsoft.com/azure/expressroute/expressroute-routing)참조하십시오.

>[!NOTE]
>새 회로의 경우 공용 피어링이 더 이상 사용되지 않습니다.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup에 대한 DPM/MABS 연결

백업이 제대로 작동하고 Azure 구독이 활성화되려면 Azure Backup 서비스에 연결되어야 합니다. 다음 표에서는 이러한 두 가지 조건이 충족되지 않을 경우에 발생하는 동작을 보여 줍니다.

**MABS-Azure 연결** | **구독** | **백업/복원**
--- | --- | ---
연결됨 | Active | DPM/MABS 디스크에 백업합니다.<br/><br/> Azure에 백업합니다.<br/><br/> 디스크에서 복원합니다.<br/><br/> Azure에서 복원합니다.
연결됨 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료된 경우 디스크 또는 Azure에서 복원할 수 있습니다.<br/><br/> 구독이 서비스 해제된 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.
15일 넘게 연결되지 않음 | Active | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 디스크 또는 Azure에서 복원할 수 있습니다.
15일 넘게 연결되지 않음 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료된 경우 디스크 또는 Azure에서 복원할 수 있습니다.<br/><br/> 구독이 서비스 해제된 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.

## <a name="dpmmabs-storage-support"></a>DPM/MABS 스토리지 지원

DPM/MABS에 백업되는 데이터는 로컬 디스크 저장소에 저장됩니다.

**스토리지** | **세부 정보**
--- | ---
**MBS** | 최신 백업 저장소(MBS)는 DPM 2016/MABS v2 이상에서 지원됩니다. MABS v1에는 사용할 수 없습니다.
**Azure VM의 MABS 스토리지** | 데이터는 DPM/MABS VM에 연결되고 DPM/MABS에서 관리되는 Azure 디스크에 저장됩니다. DPM/MABS 저장소 풀에 사용할 수 있는 디스크 수는 VM의 크기에 따라 제한됩니다.<br/><br/> A2 VM: 4 개의 디스크; A3 VM: 8개의 디스크; A4 VM: 16개의 디스크, 각 디스크에 대해 최대 크기 1TB. 이렇게 하면 사용 가능한 총 백업 저장소 풀이 결정됩니다.<br/><br/> 백업할 수 있는 데이터 양은 연결된 디스크의 수와 크기에 따라 달라집니다.
**Azure VM의 MABS 데이터 보존** | DPM/MABS Azure 디스크에서 하루 동안 데이터를 보관하고 DPM/MABS에서 더 긴 보존을 위해 볼트로 백업하는 것이 좋습니다. 이 경우 대량의 데이터를 Azure Backup으로 오프로드하여 보호할 수 있습니다.

### <a name="modern-backup-storage-mbs"></a>MBS(최신 백업 스토리지)

DPM 2016/MABS v2(Windows Server 2016에서 실행) 이후부터 최신 MBS(백업 저장소)를 활용할 수 있습니다.

- MBS 백업은 ReFS(Resilient File System) 디스크에 저장됩니다.
- MBS는 ReFS 블록 복제를 사용하여 백업 속도를 높이고 저장 공간을 보다 효율적으로 사용합니다.
- 로컬 DPM/MABS 저장소 풀에 볼륨을 추가하면 드라이브 문자로 구성합니다. 그런 후 여러 다른 볼륨에 워크로드 스토리지를 구성할 수 있습니다.
- DPM/MABS로 데이터를 백업하기 위해 보호 그룹을 만드는 경우 사용할 드라이브를 선택합니다. 예를 들어 고성능 드라이브에 SQL 또는 기타 높은 IOPS 워크로드에 대한 백업을 저장하고 성능이 낮은 드라이브에서 백업빈도가 낮은 워크로드를 저장할 수 있습니다.

## <a name="supported-backups-to-mabs"></a>MABS에 대해 지원되는 백업

Azure Backup Server를 사용하여 보호할 수 있는 다양한 서버 및 워크로드에 대한 자세한 내용은 [Azure 백업 서버 보호 매트릭스를](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix#protection-support-matrix)참조하십시오.

## <a name="supported-backups-to-dpm"></a>DPM에 대해 지원되는 백업

데이터 보호 관리자를 사용하여 보호할 수 있는 다양한 서버 및 워크로드에 대한 자세한 내용은 [DPM을 백업할 수](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019)있는 사항 문서를 참조하십시오.

- DPM/MABS에서 백업하는 클러스터된 워크로드는 DPM/MABS와 동일한 도메인또는 자식/신뢰할 수 있는 도메인에 있어야 합니다.
- 신뢰할 수 없는 도메인 또는 작업 그룹의 데이터를 백업하려면 NTLM/인증서 인증을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대한 지원되는 기능을 [검토](backup-support-matrix-mars-agent.md)합니다.
- MABS 서버를 [설정](backup-azure-microsoft-azure-backup.md)합니다.
- [DPM 을 설정합니다.](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180)
