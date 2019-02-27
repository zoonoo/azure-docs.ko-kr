---
title: Microsoft Azure Backup Server 및 System Center DPM에 대한 지원 매트릭스
description: 이 문서에서는 Microsoft Azure Backup Server 및 System Center DPM을 사용하여 온-프레미스 및 Azure VM 리소스를 백업할 때의 Azure Backup 지원을 요약해서 설명합니다.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 74b6d953939a569a3240c9d64134d143dc4f179e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431027"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-serversystem-center-dpm"></a>Microsoft Azure Backup Server/System Center DPM을 사용하는 백업에 대한 지원 매트릭스

[Azure Backup 서비스](backup-overview.md)를 사용하여 온-프레미스 컴퓨터 및 워크로드, Azure VM을 백업할 수 있습니다. 이 문서에서는 MABS(Microsoft Azure Backup Server) 또는 System Center DPM(Data Protection Manager)과 Azure Backup을 사용하여 컴퓨터를 백업할 때의 지원 설정 및 제한 사항을 요약해서 설명합니다.

## <a name="about-mabsdpm"></a>MABS/DPM 정보

[System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview?view=sc-dpm-1807)은 엔터프라이즈 컴퓨터 및 데이터의 백업 및 복구를 구성하고, 용이하게 하고, 관리하는 엔터프라이즈 솔루션입니다. 이 기능은 [System Center](https://www.microsoft.com/cloud-platform/system-center-pricing) 제품군에 속해 있습니다.


MABS(Microsoft Azure Backup Server)는 온-프레미스 물리적 서버, VM(가상 머신)과 여기에서 실행되는 앱 및 Azure VM을 백업하는 데 사용할 수 있는 서버 제품입니다.

- MABS는 System Center DPM(Data Protection Manager)을 기준으로 하며 다음과 같은 몇 가지 차이점을 제외하고 유사한 기능을 제공합니다.
    - MABS를 실행하기 위해 System Center 라이선스가 필요하지 않습니다.
    - DPM 및 MABS 둘 다, Azure에서 장기 백업 스토리지를 제공합니다. 또한 DPM을 사용하면 테이프의 장기 보관할 수 있게 데이터를 백업할 수 있습니다. MABS는 이 기능을 제공하지 않습니다.
- [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=57520)에서 MABS 서버를 다운로드할 수 있습니다. 온-프레미스 또는 Azure의 Azure VM에서 실행할 수 있습니다.

DPM 및 MABS는 다양한 앱, 서버 및 클라이언트 운영 체제의 백업을 지원합니다. 다음과 같은 여러 백업 시나리오를 제공합니다.
    - 시스템 상태 백업 또는 완전 백업을 사용하여 컴퓨터 수준에서 백업할 수 있습니다.
    - 특정 볼륨, 공유, 폴더 및 파일을 백업할 수 있습니다.
    - 최적화된 앱 인식 설정을 사용하여 특정 앱을 백업할 수 있습니다.

## <a name="mabsdpm-backup"></a>MABS/DPM 백업

MABS/DPM 및 Azure Backup을 사용하는 백업은 다음과 같이 작동합니다.

1. 백업할 각 시스템에 DPM/MABS 보호 에이전트가 설치됩니다. 
2. 컴퓨터/앱이 DPM/MABS의 로컬 스토리지에 백업됩니다.
3. MARS(Microsoft Azure Recovery Services) 에이전트가 DPM 서버/MABS에 설치됩니다.
4. MARS 에이전트는 Azure Backup을 사용하여 Azure의 백업 Recovery Services 자격 증명 모음으로 DPM/MABS 디스크를 백업합니다.

자세한 내용은 다음을 참조하세요.
- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대한 지원되는 기능을 [검토](backup-support-matrix-mars-agent.md)합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오 

**시나리오** | **에이전트** | **위치**:
--- | --- | ---
**온-프레미스 머신/워크로드 백업**: | DPM/MABS 보호 에이전트가 백업하려는 머신에서 실행됩니다.<br/><br/> MABS/DPM 서버의 MARS 에이전트 | MABS/DPM이 온-프레미스에서 실행되어야 합니다.
**Azure VM/워크로드 백업** | 보호된 컴퓨터의 DPM/MABS 보호 에이전트<br/><br/> MABS/DPM의 MARS 에이전트 | DPM/MABS는 Azure VM에서 실행되어야 합니다.

## <a name="supported-deployments"></a>지원되는 배포

DPM/MABS는 표에 요약된 것처럼 배포할 수 있습니다.

**배포** | **지원** | **세부 정보**
--- | --- | ---
**온-프레미스에 배포** | 물리적 서버<br/><br/>Hyper-V VM<br/><br/> VMware VM | DPM/MABS가 VMware VM으로 설치된 경우 VMware VM과 해당 VM에서 실행 중인 워크로드만 백업합니다.
**Azure Stack VM으로 배포** | MABS만 | DPM은 Azure Stack VM을 백업하는 데 사용할 수 없습니다.
**Azure VM으로 배포** | Azure VM과 해당 VM에서 실행 중인 워크로드를 보호합니다. | Azure에서 실행되는 MABS/DPM은 온-프레미스 컴퓨터를 백업할 수 없습니다.


## <a name="supported-mabs-and-dpm-operating-systems"></a>지원되는 MABS 및 DPM 운영 체제

Azure Backup은 다음 운영 체제 중 하나에서 실행 중인 MABS/DPM을 백업할 수 있습니다. 운영 체제는 최신 서비스 팩 및 업데이트를 실행하고 있어야 합니다.

**시나리오** | **MABS/DPM** 
--- | --- 
**Azure VM의 MABS** | Windows Server 2012 R2<br/><br/> Windows 2016 Datacenter<br/><br/> Windows 2019 Datacenter<br/><br/> 먼저 Marketplace의 이미지로 시작하는 것이 좋습니다.<br/><br/> 2코어 및 3.5GB RAM이 있는 최소 A2 Standard 
**Azure VM의 DPM** | System Center 2012 R2 업데이트 3 이상<br/><br/> [System Center에 필요한](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server) Windows 운영 체제<br/><br/> 먼저 Marketplace의 이미지로 시작하는 것이 좋습니다.<br/><br/> 2코어 및 3.5GB RAM이 있는 최소 A2 Standard 
**MABS 온-프레미스** | 지원되는 64비트 운영 체제:<br/><br/> - MABS v3 이상: Windows Server 2019(Standard, Datacenter, Essentials) <br/><br/> MABS v2 이상: Windows Server 2016(Standard, Datacenter, Essentials)<br/><br/> 모든 MABS 버전: Windows Server 2012 R2/2012(Standard, Datacenter, Foundation)<br/><br/>모든 MABS 버전: Windows Storage Server 2012 R2/2012(Standard/Workgroup)
**DPM 온-프레미스** | 물리적 서버/Hyper-V VM: System Center 2012 SP1 이상<br/><br/> VMware VM: System Center 2012 R2 업데이트 5 이상 



## <a name="management-support"></a>관리 지원
**문제** | **세부 정보**
--- | ---
**설치** | DPM/MABS는 단일 용도 컴퓨터에 설치해야 합니다.<br/><br/> 도메인 컨트롤러, 애플리케이션 서버 역할이 설치된 컴퓨터, Exchange Server 또는 System Center Operations Manager를 실행하는 컴퓨터 또는 클러스터 노드에는 DPM/MABS를 설치하지 마세요.<br/><br/> 모든 DPM 시스템 요구 사항을 [검토](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1807#dpm-server)합니다.
**도메인** | DPM 서버/MABS는 도메인에 가입되어야 합니다. DPM/MABS를 먼저 설치한 후 도메인에 가입합니다. 배포가 지원되지 않으면 DPM/MABS를 새 도메인으로 이동
**Storage** | MBS(최신 백업 스토리지)는 DPM 2016/MABS v2부터 지원됩니다. MABS v1에는 사용할 수 없습니다.
**MABS 업그레이드** | MABS v3를 직접 설치하거나 MABS v2에서 MABS v3로 업그레이드할 수 있습니다. [자세히 알아보기](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**MABS 이동** | MBS를 사용하는 경우 스토리지 보존이 지원될 때 MABS를 새 서버로 이동<br/><br/> 새 서버는 원래 서버와 이름이 같아야 합니다. 동일한 스토리지 풀을 유지하고 동일한 MABS 데이터베이스를 사용하여 데이터 복구 지점을 저장하려는 경우에는 이름을 변경할 수 없습니다.<br/><br/> MABS 데이터베이스를 복원해야 하므로 백업이 필요합니다.




## <a name="mabs-support-on-azure-stack"></a>Azure Stack의 MABS 지원

단일 위치에서 Azure Stack VM 및 워크로드 백업을 관리할 수 있도록 Azure Stack VM에 MABS를 배포할 수 있습니다.

**구성 요소** | **세부 정보**
--- | --- 
**Azure Stack VM의 MABS** | A2 크기 이상입니다. 먼저 Azure Marketplace의 Windows Server 2012 R2 또는 Windows Server 2016 이미지로 시작하는 것이 좋습니다.<br/><br/> MABS VM에 다른 항목은 설치하지 않는 것이 좋습니다.
**MABS 스토리지** | MAB VM에 대해 별도의 스토리지 계정을 사용합니다. MABS에서 실행되는 MARS 에이전트는 캐시 위치로 사용되고 클라우드의 데이터 복원을 저장할 임시 스토리지가 필요합니다.
**MABS 스토리지 풀** | MABS 스토리지 풀의 크기는 MABS VM에 연결된 디스크의 수와 크기에 따라 결정됩니다. 각 Azure Stack VM 크기는 최대 디스크 수를 갖습니다. 예를 들어 A2는 4개의 디스크입니다.
**MABS 보존** | 백업한 데이터를 5일 넘게 로컬 MABS 디스크에 보존하지 않아야 합니다.
**MABS 강화** | 배포를 강화하기 위해 MABS VM의 크기를 늘릴 수 있습니다. 예를 들어 A에서 D 시리즈로 늘립니다.<br/><br/> 또한 백업 데이터를 Azure에 정기적으로 오프하고 있는지 확인하고 필요한 경우 추가 MABS 서버를 배포할 수 있습니다.
MABS의 .NET Framework | MABS VM에는 .NET Framework 3.3 SP1 이상을 설치해야 합니다.
**MABS 도메인** | MABS VM은 도메인에 가입되어 있어야 합니다. 관리자 권한이 있는 도메인 사용자는 VM에 MABS를 설치해야 합니다.
**Azure Stack VM 데이터 백업** | 파일, 폴더 및 앱을 백업할 수 있습니다.
**지원되는 백업** | 백업하려는 VM에서 다음 운영 체제가 지원됩니다.<br/><br/> - Windows Server 반기 채널(Datacenter/Enterprise/Standard)<br/><br/> Windows Server 2016/2012 R2/2012/2008 R2
**Azure Stack VM에 대한 SQL 지원** | SQL Server 2016/2014/2012 SP1을 백업합니다.<br/><br/> 데이터베이스를 백업 및 복구합니다.
**Azure Stack VM에 대한 SharePoint 지원** | SharePoint 2016/2013/2010<br/><br/> 팜, 데이터베이스, 프런트 엔드, 웹 서버를 백업 및 복구합니다.
**백업한 VM에 대한 네트워크 요구 사항** | Azure Stack 워크로드의 모든 VM은 동일한 VNet에 연결되고 동일한 구독에 속해야 합니다.


## <a name="dpmmabs-networking-support"></a>DPM/MABS 네트워킹 지원

### <a name="url-access"></a>URL 액세스

DPM 서버/MABS는 다음 URL에 액세스할 수 있어야 합니다.

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Azure Backup에 대한 DPM/MABS 연결

백업이 제대로 작동하고 Azure 구독이 활성화되려면 Azure Backup 서비스에 연결되어야 합니다. 다음 표에서는 이러한 두 가지 조건이 충족되지 않을 경우에 발생하는 동작을 보여 줍니다.

**MABS-Azure 연결** | **구독** | **백업/복원** 
--- | --- | --- 
연결됨 | Active | DPM/MABS 디스크에 백업<br/><br/> Azure에 Backup<br/><br/> 디스크에서 복구<br/><br/> Azure에서 복구
연결됨 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료된 경우 디스크 또는 Azure에서 복원할 수 있습니다.<br/><br/> 구독이 서비스 해제된 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.
15일 넘게 연결되지 않음 | Active | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 디스크 또는 Azure에서 복원할 수 있습니다.
15일 넘게 연결되지 않음 | 만료됨/프로비전 해제됨 | 디스크 또는 Azure에 대한 백업이 없습니다.<br/><br/> 구독이 만료된 경우 디스크 또는 Azure에서 복원할 수 있습니다.<br/><br/> 구독이 서비스 해제된 경우 디스크 또는 Azure에서 복원할 수 없습니다. Azure 복구 지점이 삭제됩니다.



## <a name="dpmmabs-storage-support"></a>DPM/MABS 스토리지 지원

DPM/MABS에 백업된 데이터는 로컬 디스크 스토리지에 저장됩니다. 

**Storage** | **세부 정보**
--- | ---
**MBS** | MBS(최신 백업 스토리지)는 DPM 2016/MABS v2부터 지원됩니다. MABS v1에는 사용할 수 없습니다.
**Azure VM의 MABS 스토리지** | 데이터는 DPM/MABS VM에 연결되고 DPM/MABS에서 관리되는 Azure 디스크에 저장됩니다. DPM/MABS 스토리지 풀에 사용할 수 있는 디스크 수는 VM 크기에 따라 제한됩니다.<br/><br/> A2 VM: 4개의 디스크, A3 VM: 8개의 디스크, A4 VM: 16개의 디스크(디스크당 최대 1TB 크기) 이에 따라 사용 가능한 총 백업 스토리지 풀이 결정됩니다.<br/><br/> 백업할 수 있는 데이터 양은 연결된 디스크의 수와 크기에 따라 달라집니다.
**Azure VM의 MABS 데이터 보존** | 하루 동안 DPM/MABS Azure 디스크에 데이터를 유지하고, 더 오래 보존하려는 경우 DPM/MABS에서 자격 증명 모음으로 백업하는 것이 좋습니다. 이 경우 대량의 데이터를 Azure Backup으로 오프로드하여 보호할 수 있습니다.


### <a name="modern-backup-storage-mbs"></a>MBS(최신 백업 스토리지)
DPM 2016/MABS v2부터(Windows Server 2016에서 실행) MBS(최신 백업 스토리지)를 활용할 수 있습니다.

- MBS 백업은 ReFS(Resilient File System) 디스크에 저장됩니다.
- MBS는 더 빠른 백업과 보다 효율적인 스토리지 공간 사용을 위해 ReFS 블록 복제를 사용합니다.
- 로컬 DPM/MABS 스토리지 풀에 볼륨을 추가한 경우 드라이브 문자를 사용하여 구성했을 것입니다. 그런 후 여러 다른 볼륨에 워크로드 스토리지를 구성할 수 있습니다.
- DPM/MABS로 데이터를 백업하기 위해 보호 그룹을 만드는 경우 사용할 드라이브를 선택합니다. 예를 들어, SQL 또는 기타 높은 IOPS 워크로드의 백업은 고성능 드라이브에 저장하고, 덜 자주 백업하는 워크로드는 저성능 드라이브에 저장할 수 있습니다.


## <a name="supported-backups-to-mabs"></a>MABS에 대해 지원되는 백업

다음 표에서는 온-프레미스 컴퓨터 및 Azure VM에서 MABS로 백업할 수 있는 항목을 요약해서 보여 줍니다.


**Backup** | **버전** | **MABS** | **세부 정보**
--- | --- | --- | ---
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64비트) | MABS v3, v2, V1 | 온-프레미스 | 볼륨/공유/폴더/파일<br/><br/> 중복 제거된 볼륨 지원<br/><br/> 볼륨은 1GB 및 NTFS 이상이어야 합니다.
**Windows Server 2016(Datacenter, Standard)(Nano 제외)**<br/><br/> 64/32비트 | MABS v3, v2 | 온-프레미스/Azure VM| 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> 중복 제거된 볼륨 지원
**Windows Server 2012 R2(Datacenter 및 Standard)**<br/><br/> 64/32비트 | MABS v3, v2, v1 | 온-프레미스/Azure VM | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> 중복 제거된 볼륨 지원
**Windows Server 2012 SP1(Datacenter 및 Standard)**<br/><br/> 64/32비트 | MABS v3, v2, v1<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치해야 합니다. | 온-프레미스/Azure VM | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> 중복 제거된 볼륨 지원
**Windows Server 2012(Datacenter 및 Standard)**<br/><br/> 64/32비트 | MABS v1 | 온-프레미스/Azure VM | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> 중복 제거된 볼륨 지원
**Windows 2008 R2 SP1(Standard 및 Enterprise)**<br/><br/> 64/32비트 | MABS v3, v2, v1에서 지원됩니다.<br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치해야 합니다. | 온-프레미스/Azure VM |   **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> 중복 제거된 볼륨 지원
**Windows 2008 R2(Standard 및 Enterprise)**<br/><br/> 64/32비트 | MABS v1 MABS v2/v3에서는 OS에서 SP1을 실행해야 합니다. | 온-프레미스/Azure VM | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> 중복 제거된 볼륨 지원
**Windows Server 2008 SP2**<br/><br/> 64/32비트 | MABS v1, v2, v3 | MABS를 온-프레미스 물리적 컴퓨터 또는 Hyper-V VM으로 배포하면 v1만 지원됩니다.<br/><br/> MABS를 VMware VM으로 배포하면 MABS v1, 2, 3이 지원됩니다.<br/><br/> Azure VM에서 실행되는 MABS에서는 지원되지 않습니다. | 볼륨/공유/폴더/파일/시스템 상태/완전 복구
**Windows Storage Server 2008** | MABS v1, v2, v3 | 온-프레미스 물리적 서버/Hyper-V VM로 배포한 MABS | Azure VM에서 실행되는 MABS에서는 지원되지 않습니다. | 볼륨/공유/폴더/파일/시스템 상태/완전 복구
**SQL Server 2017** | MABS v3 | 온-프레미스/Azure VM| SQL Server 데이터베이스를 백업합니다.<br/><br/> SQL Server 클러스터 백업이 지원됩니다.<br/><br/>CSV에 저장된 데이터베이스는 지원되지 않습니다.
**SQL Server 2016/2016 SP1** | MABS v3, v2 | 온-프레미스/Azure VM| SQL Server 데이터베이스를 백업합니다.<br/><br/> SQL Server 클러스터 백업이 지원됩니다.<br/><br/>CSV에 저장된 데이터베이스는 지원되지 않습니다.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | MABS v3, v2, v1 | 온-프레미스/Azure VM| SQL Server 데이터베이스를 백업합니다.<br/><br/> SQL Server 클러스터 백업이 지원됩니다.<br/><br/>CSV에 저장된 데이터베이스는 지원되지 않습니다.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | MABS v3, v2, V1 | 온-프레미스 | DAG에서 독립 실행형 Exchange Server, 데이터베이스를 백업합니다.<br/><br/> DAG에서 사서함, 사서함 데이터베이스를 복구합니다.<br/><br/> ReFS는 지원되지 않습니다.<br/><br/> 비공유 디스크 클러스터를 백업합니다.<br/><br/> 연속 복제용으로 구성된 Exchange Server를 백업합니다.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | MABS v3, v2, v1 | 온-프레미스/Azure VM | 팜, 프런트 엔드 웹 서버를 백업합니다.<br/><br/> 팜, 데이터베이스, 웹앱, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버를 복구합니다.<br/><br/> 콘텐츠 데이터베이스에 대해 AlwaysOn을 사용하는 팜은 백업할 수 없습니다.
**Windows Server 2016의 Hyper-V**<br/><br/> **Windows Server 2012 R2/2012**(Datacenter/Standard)<br/><br/> **Windows Server 2008 R2(SP1)** | MABS v3, v2, v1 | 온-프레미스 | **Hyper-V 호스트의 MABS 에이전트**: 전체 VM 및 호스트 데이터 파일을 백업합니다. 로컬 스토리지로 VM을, CSV 스토리지로 클러스터의 VM을, SMB 파일 서버 스토리지로 VM을 백업합니다.<br/><br/> **게스트 VM의 MABS 에이전트**: VM에서 실행 중인 워크로드를 백업합니다. CSV<br/><br/> **복구**: VM, VHD/볼륨/폴더/파일의 항목 수준 복구<br/><br/> **Linux VM**: Hyper-V가 Windows Server 2012 R2 이상에서 실행 중일 때 백업합니다. Linux VM에 대한 복구는 전체 컴퓨터에 해당합니다.
**VMware VM: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> MABS v1에는 업데이트 롤업 1이 필요합니다. | 온-프레미스 | CSV, NFS 및 SAN 스토리지에 VMware VM을 백업합니다.<br/><br/> 전체 VM을 복구합니다.<br/><br/> Windows/Linux 백업<br/><br/> Windows VM에 대해서만 폴더/파일의 항목 수준 복구<br/><br/> VMware vApp은 지원되지 않습니다.<br/><br/> Linux VM에 대한 복구는 전체 컴퓨터에 해당합니다.



## <a name="supported-backups-to-dpm"></a>DPM에 대해 지원되는 백업

다음 표에서는 온-프레미스 컴퓨터 및 Azure VM에서 DPM으로 백업할 수 있는 항목을 요약해서 보여 줍니다.



**Backup** | **DPM** | **세부 정보**
--- | --- | --- 
**Windows 10, 8.1, 8, 7**<br/><br/>(32/64비트) | 온-프레미스만<br/><br/> DPM 2012 R2를 사용하여 Windows 10을 백업하는 경우 [업데이트 11](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)을 설치하는 것이 좋습니다. | 볼륨/공유/폴더/파일<br/><br/> 중복 제거된 볼륨 지원<br/><br/> 볼륨은 1GB 및 NTFS 이상이어야 합니다.
**Windows Server 2016(Datacenter, Standard)(Nano 제외)**<br/><br/> 64/32비트 | 온-프레미스/Azure VM<br/><br/> DPM 2016만 해당| 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> 중복 제거된 볼륨 지원
**Windows Server 2012 R2(Datacenter 및 Standard)**<br/><br/> 64/32비트 | 온-프레미스/Azure VM | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> DPM 2012 R2부터 중복 제거된 볼륨이 지원됩니다.
**Windows Server 2012 SP1(Datacenter 및 Standard)**<br/><br/> 64/32비트 | 온-프레미스/Azure VM | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> DPM 2012 R2부터 중복 제거된 볼륨이 지원됩니다.
**Windows Server 2012(Datacenter 및 Standard)**<br/><br/> 64/32비트 |  온-프레미스/Azure VM | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일<br/><br/> DPM 2012 R2부터 중복 제거된 볼륨이 지원됩니다.
**Windows 2008 R2 SP1(Standard 및 Enterprise)**<br/><br/> 64/32비트 | 온-프레미스/Azure VM <br/><br/> [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855)을 설치해야 합니다. |   **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구<br/><br/> **Azure VM 보호**: 볼륨/공유/폴더/파일
**Windows 2008 R2(Standard 및 Enterprise)**<br/><br/> 64/32비트 | 온-프레미스<br/><br/> DPM은 VMware VM으로 설치할 수 없습니다.<br/><br/> Azure VM에서 실행되는 DPM은 지원되지 않습니다. | **온-프레미스 보호**: 볼륨/공유/폴더/파일/시스템 상태/완전 복구
**Windows Server 2008 SP2**<br/><br/> 64/32비트 | 온-프레미스만<br/><br/> DPM은 VMware VM으로 실행하는 경우 지원됩니다. 물리적 서버 또는 Hyper-V VM으로 실행하는 것은 지원되지 않습니다. | 볼륨/공유/폴더/파일/시스템 상태/완전 복구
**Windows Storage Server 2008** | 물리적 서버 또는 Hyper-V VM으로 실행하는 DPM 온-프레미스 | 볼륨/공유/폴더/파일/시스템 상태/완전 복구
**SQL Server 2017** | DPM SAC, 업데이트 롤업 5 이상을 실행하는 DPM 2016<br/><br/> 온-프레미스/Azure VM| SQL Server 데이터베이스를 백업합니다.<br/><br/> SQL Server 클러스터 백업이 지원됩니다.<br/><br/>CSV에 저장된 데이터베이스는 지원되지 않습니다.
**SQL Server 2016 SP1** | DPM 2012 R2에는 지원되지 않습니다. DPM SAC, 업데이트 롤업 4 이상을 실행하는 DPM 2016에는 지원됩니다.<br/><br/> 온-프레미스/Azure VM| SQL Server 데이터베이스를 백업합니다.<br/><br/> SQL Server 클러스터 백업이 지원됩니다.<br/><br/>CSV에 저장된 데이터베이스는 지원되지 않습니다.
**SQL Server 2016** | DPM 2012 R2에는 지원되지 않습니다. DPM SAC, 업데이트 롤업 2 이상의 DPM 2016에는 지원됩니다.<br/><br/> 온-프레미스/Azure VM| SQL Server 데이터베이스를 백업합니다.<br/><br/> SQL Server 클러스터 백업이 지원됩니다.<br/><br/>CSV에 저장된 데이터베이스는 지원되지 않습니다.
**SQL Server 2014**<br/><br/> **SQL Server 2012/SP1/SP2**<br/><br/> **SQL Server 2008 R2**<br/><br/> **SQL Server 2008** | 업데이트 롤업 4 이상을 실행하는 DPM 2012 R2가 있는 SQL Server 2014<br/><br/> 온-프레미스/Azure VM| SQL Server 데이터베이스를 백업합니다.<br/><br/> SQL Server 클러스터 백업이 지원됩니다.<br/><br/>CSV에 저장된 데이터베이스는 지원되지 않습니다.
**Exchange 2016**<br/><br/> **Exchange 2013**<br/><br/> **Exchange 2010** | Exchange 2016의 경우 DPM 2012 R2에 업데이트 롤업 9 이상이 필요합니다.<br/><br/> 온-프레미스 | DAG에서 독립 실행형 Exchange Server, 데이터베이스를 백업합니다.<br/><br/> DAG에서 사서함, 사서함 데이터베이스를 복구합니다.<br/><br/> ReFS는 지원되지 않습니다.<br/><br/> 비공유 디스크 클러스터를 백업합니다.<br/><br/> 연속 복제용으로 구성된 Exchange Server를 백업합니다.
**SharePoint 2016**<br/><br/> **Sharepoint 2013**<br/><br/> **SharePoint 2010** | DPM 2016 이상의 SharePoint 2016<br/><br/>온-프레미스/Azure VM | 팜, 프런트 엔드 웹 서버를 백업합니다.<br/><br/> 팜, 데이터베이스, 웹앱, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버를 복구합니다.<br/><br/> 콘텐츠 데이터베이스에 대해 AlwaysOn을 사용하는 팜은 백업할 수 없습니다.
**Windows Server 2016의 Hyper-V**<br/><br/> **Windows Server 2012 R2/2012**(Datacenter/Standard)<br/><br/> **Windows Server 2008 R2(SP1)** | DPM 2016부터 2016의 Hyper-V가 지원됩니다.<br/><br/> 온-프레미스 | **Hyper-V 호스트의 MABS 에이전트**: 전체 VM 및 호스트 데이터 파일을 백업합니다. 로컬 스토리지로 VM을, CSV 스토리지로 클러스터의 VM을, SMB 파일 서버 스토리지로 VM을 백업합니다.<br/><br/> **게스트 VM의 MABS 에이전트**: VM에서 실행 중인 워크로드를 백업합니다. CSV<br/><br/> **복구**: VM, VHD/볼륨/폴더/파일의 항목 수준 복구<br/><br/> **Linux VM**: Hyper-V가 Windows Server 2012 R2 이상에서 실행 중일 때 백업합니다. Linux VM에 대한 복구는 전체 컴퓨터에 해당합니다.
**VMware VM: vCenter/vSphere ESXi 5.5/6.0/6.5** | MABS v3, v2, v1.<br/><br/> DPM 2012 R2에는 System Center 업데이트 롤업 1이 필요합니다. | 온-프레미스 | CSV, NFS 및 SAN 스토리지에 VMware VM을 백업합니다.<br/><br/> 전체 VM을 복구합니다.<br/><br/> Windows/Linux 백업<br/><br/> Windows VM에 대해서만 폴더/파일의 항목 수준 복구<br/><br/> VMware vApp은 지원되지 않습니다.<br/><br/> Linux VM에 대한 복구는 전체 컴퓨터에 해당합니다.


- DPM/MABS로 백업한 클러스터형 워크로드는 DPM/MABS와 동일한 도메인이나 자식/신뢰할 수 있는 도메인에 있어야 합니다. 
- 신뢰할 수 없는 도메인 또는 작업 그룹의 데이터를 백업하려면 NTLM/인증서 인증을 사용할 수 있습니다.



## <a name="next-steps"></a>다음 단계

- MABS 아키텍처에 대해 [자세히 알아봅니다](backup-architecture.md#architecture-back-up-to-dpmmabs).
- MARS 에이전트에 대한 지원되는 기능을 [검토](backup-support-matrix-mars-agent.md)합니다.
- MABS 서버를 [설정](backup-azure-microsoft-azure-backup.md)합니다.
- [설정](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-180
