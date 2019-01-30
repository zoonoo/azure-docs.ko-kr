---
title: Azure Backup 아키텍처
description: Azure Backup 서비스에서 사용하는 아키텍처, 구성 요소 및 프로세스에 대한 개요를 제공합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: raynew
ms.openlocfilehash: 84890c0658970aa9f61a06764cf902a5e5ee4379
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812568"
---
# <a name="azure-backup-architecture"></a>Azure Backup 아키텍처

[Azure Backup 서비스](backup-overview.md)를 사용하여 Microsoft Azure 클라우드에 데이터를 백업할 수 있습니다. 이 문서에는 Azure Backup 아키텍처, 구성 요소 및 프로세스가 요약되어 있습니다. 


## <a name="what-does-azure-backup-do"></a>Azure Backup의 기능은 무엇인가요?

Azure Backup은 온-프레미스 머신과 Azure VM에서 실행되는 데이터, 머신 상태 및 워크로드를 백업합니다. 여러 Azure Backup 시나리오가 있습니다.

- **온-프레미스 머신 백업**:
    - Azure Backup을 사용하여 온-프레미스 머신을 Azure에 직접 백업할 수 있습니다.
    - System Center DPM(Data Protection Manager) 또는 MABS(Microsoft Azure Backup Server)를 사용하여 온-프레미스 머신을 보호한 다음, Azure Backup을 사용하여 보호된 데이터를 DPM/MABS에 백업할 수 있습니다.
- **Azure VM 백업**:
    - Azure Backup을 사용하여 Azure VM을 직접 백업할 수 있습니다.
    - Azure에서 실행되는 DPM 또는 MABS를 사용하여 Azure VM을 보호하고, Azure Backup을 사용하여 DPM/MABS 데이터에 보호된 데이터를 백업할 수 있습니다.

[백업할 수 있는 내용](backup-overview.md) 및 [지원되는 백업 시나리오](backup-support-matrix.md)에 대해 자세히 알아봅니다.


## <a name="where-is-data-backed-up"></a>데이터는 어디에 백업되나요?

Azure Backup은 백업된 데이터를 Recovery Services 자격 증명 모음에 저장합니다. 자격 증명 모음은 백업 복사본, 복구 지점 및 백업 정책과 같은 데이터를 보관하는 데 사용되는 Azure의 온라인 스토리지 엔터티입니다.

- Recovery Services 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다.
- 각 Azure 구독에서 최대 500개의 Recovery Services 자격 증명 모음을 만들 수 있습니다. 
- Azure VM 및 온-프레미스 머신을 포함하여 자격 증명 모음에 백업된 항목을 모니터링할 수 있습니다.
- Azure [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 사용하여 자격 증명 모음 액세스를 관리할 수 있습니다.
- 자격 증명 모음의 데이터가 중복성을 위해 복제되는 방법을 지정합니다.
    - **LRS**: LRS(로컬 중복 스토리지)를 사용하여 데이터 센터의 장애에 대해 보호할 수 있습니다. LRS는 스토리지 배율 단위에 데이터를 복제합니다. [자세히 알아보기](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS**: GRS(지역 중복 스토리지)를 사용할 수 있습니다. 지역 전체 중단으로부터 보호합니다. 데이터를 보조 지역에 복제합니다. [자세히 알아보기](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - 기본적으로 Backup용 Recovery Services 자격 증명 모음은 GRS를 사용합니다. 



## <a name="how-does-azure-backup-work"></a>Azure Backup은 어떻게 작동하나요?

Azure Backup은 기본 또는 사용자 지정 백업 정책에 따라 백업 작업을 실행합니다. Azure Backup에서 백업을 수행하는 방법은 시나리오에 따라 다릅니다.

**시나리오** | **세부 정보** 
--- | ---
**온-프레미스 머신에 직접 백업** | 온-프레미스 머신에 직접 백업하기 위해 Azure Backup은 MARS(Microsoft Azure Recovery Services) 에이전트를 사용합니다. 백업할 각 머신에 에이전트가 설치됩니다. <br/><br/> 이 유형의 백업은 온-프레미스 Linux 시스템에 사용할 수 없습니다. 
**Azure VM 직접 백업** | Azure VM을 직접 백업하기 위해 VM에 대해 백업을 처음 실행할 때 Azure VM 확장이 VM에 설치됩니다. 
**DPM 또는 MABS를 통해 보호된 머신 및 앱 백업** | 이 시나리오에서는 머신/앱이 DPM 또는 MABS 로컬 스토리지에 먼저 백업됩니다. 그런 다음, DPM/MABS의 데이터가 Azure Backup을 통해 자격 증명 모음에 백업됩니다. 온-프레미스 머신은 온-프레미스에서 실행되는 DPM/MABS를 통해 보호할 수 있습니다. Azure VM은 Azure에서 실행되는 DPM/MABS를 통해 보호할 수 있습니다.

[개요를 확인](backup-overview.md)하고, 각 시나리오에 대해 [지원되는 내용](backup-support-matrix.md)을 참조하세요.


### <a name="backup-agents"></a>백업 에이전트

Azure Backup은 백업 유형에 따라 다른 에이전트를 제공합니다.

**에이전트** | **세부 정보** 
--- | --- 
**MARS(Microsoft Azure Recovery Services) 에이전트** | 이 에이전트는 파일, 폴더 및 시스템 상태를 백업하기 위해 개별 온-프레미스 Windows Server에서 실행됩니다.<br/><br/> 이 에이전트는 DPM/MABS 로컬 스토리지 디스크를 백업하기 위해 DPM/MABS 서버에서 실행됩니다. 머신과 앱은 이 DPM/MABS 디스크에 로컬로 백업됩니다.
**Azure VM 확장** | Azure VM을 백업하기 위해 VM에서 실행되는 Azure VM 에이전트에 백업 확장이 추가됩니다. 


## <a name="backup-types"></a>Backup 유형

**백업 유형** | **세부 정보** | **사용 현황**
--- | --- | ---
**전체** | 백업에는 전체 데이터 원본이 포함됩니다.<br/><br/> 전체 백업은 네트워크 대역폭을 더 많이 사용합니다. | 초기 백업에 사용됩니다.
**차등** |  초기 전체 백업 이후에 변경된 블록을 저장합니다. 더 작은 네트워크 및 스토리지를 사용하며, 변경되지 않은 데이터의 중복 복사본을 보존하지 않습니다.<br/><br/> 후속 백업 간에 변경되지 않은 데이터 블록이 전송되어 저장되므로 비효율적입니다. | Azure Backup에서 사용되지 않습니다.
**증분** | 스토리지 및 네트워크 효율성이 높습니다. 이전 백업 이후 변경된 데이터 블록만 저장합니다. <br/><br/> 증분 백업의 경우 전체 백업으로 보완할 필요가 없습니다. | DPM/MABS에서 디스크 백업에 사용되며 Azure에 대한 모든 백업에서 사용됩니다.

### <a name="comparison"></a>비교

스토리지 사용량, RTO(복구 시간 목표) 및 네트워크 사용량은 각 백업 유형마다 다릅니다. 다음 이미지는 백업 유형을 비교해서 보여 줍니다.
- 데이터 원본 A는 매달 백업되는 10개의 스토리지 블록 A1-A10으로 구성됩니다.
- 블록 A2, A3, A4 및 A9는 첫 번째 달에 변경되고 블록 A5는 다음 달에 변경됩니다.
- 차등 백업의 경우 두 번째 달에 변경된 블록 A2, A3, A4 및 A9가 백업됩니다. 세 번째 달에 이와 동일한 블록이 변경된 블록 A5와 함께 다시 백업됩니다. 변경된 블록은 다음 전체 백업이 발생할 때가지 계속 백업됩니다.
- 증분 백업의 경우 첫 번째 달에 전체 백업이 수행된 후 A2, A3, A4 및 A9 블록이 변경된 것으로 표시되고, 두 번째 달에 전송되었습니다. 세 번째 달에는 변경된 블록 A5만 표시되고 전송됩니다. 

![백업 방법에 대한 비교를 보여주는 이미지](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>백업 기능

다음 표에는 다양한 백업 유형의 기능이 요약되어 있습니다.

**기능** | **온-프레미스 Windows 머신(직접)** | **Azure VM** | **DPM/MABS가 있는 머신/앱**
--- | --- | --- | ---
자격 증명 모음에 백업 | ![예][green] | ![예][green] | ![예][green] 
DPM/MABS 디스크에 백업한 후 Azure에 백업 | | | ![예][green] 
백업을 위해 전송된 데이터 압축 | ![예][green] | 데이터를 전송할 때 압축이 사용되지 않습니다. 스토리지가 약간 증가하지만 복원 속도가 더 빠릅니다.  | ![예][green] 
증분 백업 실행 |![예][green] |![예][green] |![예][green] 
중복 제거된 디스크 백업 | | | ![부분적으로][yellow]<br/><br/> 온-프레미스에 배포된 DPM/MABS 서버에만 해당합니다. 

![테이블 키](./media/backup-architecture/table-key.png)


## <a name="architecture-direct-backup-of-on-premises-windows-machines"></a>아키텍처: 온-프레미스 Windows 머신의 직접 백업

1. 시나리오를 설정하려면 머신에서 MARS(Microsoft Azure Recovery Services) 에이전트를 다운로드하여 설치하고 백업할 항목, 백업이 실행될 시기 및 Azure에 보존되는 기간을 선택합니다.
2. 초기 백업은 백업 설정에 따라 실행됩니다.
3. MARS 에이전트는 Windows VSS(볼륨 섀도 복사본) 서비스를 사용하여 백업용으로 선택된 볼륨의 특정 시점 스냅숏을 만듭니다.
    - MARS 에이전트는 Windows 시스템 쓰기만 사용하여 스냅숏을 캡처합니다.
    - 에이전트는 애플리케이션 VSS 기록기를 사용하지 않으므로 앱 일관성이 있는 스냅숏을 캡처하지 않습니다.
3. VSS를 사용하여 스냅숏을 만든 후 MARS 에이전트는 백업을 구성할 때 지정한 캐시 폴더에 VHD를 만들고 각 데이터 블록에 대한 체크섬을 저장합니다. 
4. 임시 백업을 실행하지 않는 한, 지정한 일정에 따라 증분 백업이 실행됩니다.
5. 증분 백업에서는 변경된 파일이 식별되고 새 VHD가 만들어집니다. 압축되고 암호화된 다음, 자격 증명 모음으로 전송됩니다.
6. 증분 백업이 완료되면 새 VHD가 초기 복제 이후에 만든 VHD와 병합되어 지속적인 백업을 위한 비교에 사용할 최신 상태를 제공합니다. 

![MARS 에이전트를 사용한 온-프레미스 Windows 서버 백업](./media/backup-architecture/architecture-on-premises-mars.png)


## <a name="architecture-direct-backup-of-azure-vms"></a>아키텍처: Azure VM의 직접 백업

1. Azure VM에 대해 백업을 사용하도록 설정하면 지정한 일정에 따라 백업이 실행됩니다.
2. 첫 번째 백업 중에 VM이 실행 중인 경우 백업 확장이 VM에 설치됩니다.
    - Windows VM의 경우 VMSnapshot 확장이 설치됩니다.
    - Linux VM의 경우 VMSnapshot Linux 확장이 설치됩니다.
3. 확장은 스토리지 수준의 스냅숏을 만듭니다. 
    - 실행 중인 Windows VM의 경우 Backup이 VSS와 조정되어 VM의 앱 일관성이 있는 스냅숏을 만듭니다. 기본적으로 Backup은 전체 VSS 백업을 만듭니다. Backup에서 앱 일관성이 있는 스냅숏을 만들 수 없는 경우에는 일관성 있는 파일 스냅숏을 만듭니다.
    - Linux VM의 경우 Backup에서 파일 일관성이 있는 백업을 수행합니다. 앱 일관성이 있는 스냅숏의 경우 수동으로 사전/사후 스크립트를 사용자 지정해야 합니다.
    - 각 VM 디스크를 병렬로 백업하여 백업이 최적화됩니다. 백업 중인 각 디스크에 대해 Azure Backup은 디스크의 블록을 읽고 변경된 데이터만 저장합니다. 
4. 스냅숏을 만든 후에는 데이터가 자격 증명 모음으로 전송됩니다. 
    - 마지막 백업 이후 변경된 데이터 블록만 복사됩니다.
    - 데이터가 암호화되지 않습니다. Azure Backup은 ADE(Azure Disk Encryption)를 사용하여 암호화된 Azure VM을 백업할 수 있습니다.
    - 스냅숏 데이터가 자격 증명 모음에 즉시 복사되지 않을 수 있습니다. 사용량이 많은 시간에는 몇 시간이 걸릴 수 있습니다. VM에 대한 총 백업 시간은 일별 백업 정책의 경우 24시간 미만입니다.
5. 데이터가 자격 증명 모음으로 전송된 후 스냅숏이 제거되고 복구 지점이 만들어집니다.


![Azure VM의 백업](./media/backup-architecture/architecture-azure-vm.png)


## <a name="architecture-back-up-to-dpmmabs"></a>아키텍처: DPM/MABS에 백업

1. 보호하려는 머신에 DPM 또는 MABS 보호 에이전트를 설치하고 DPM 보호 그룹에 머신을 추가합니다.
    - 온-프레미스 머신을 보호하려면 DPM 또는 MABS 서버가 온-프레미스에 있어야 합니다.
    - Azure VM을 보호하려면 Azure VM으로 실행되는 DPM 또는 MABS 서버가 Azure에 있어야 합니다.
    - DPM/MABS를 사용하여 백업 볼륨, 공유, 파일 및 폴더를 보호할 수 있습니다. 머신 시스템 상태/완전 복구를 보호하고 특정 앱을 앱 인식 백업 설정으로 보호할 수 있습니다.
2. DPM에서 머신 또는 앱에 대한 보호를 설정할 때 단기 스토리지용 DPM 로컬 디스크에 백업한 다음, Azure(온라인 보호)에 백업하도록 선택합니다. 로컬 DPM/MABS 스토리지에 백업을 실행할 시기와 Azure에 온라인 백업을 실행할 시기도 지정합니다.
3. 보호된 워크로드의 디스크는 지정한 일정에 따라 로컬 DPM 디스크에 백업한 후 Azure에 백업됩니다.
4. 자격 증명 모음에 온라인 백업은 DPM/MABS 서버에서 실행되는 MARS 에이전트에 의해 처리됩니다.

![DPM 또는 MABS로 보호된 머신/워크로드의 백업](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Azure VM 스토리지

- Azure VM은 디스크를 사용하여 운영 체제, 앱 및 데이터를 저장합니다.
- Azure VM에는 두 개 이상의 디스크가 있습니다. 운영 체제용 디스크와 임시 디스크입니다. 앱 데이터에 사용되는 데이터 디스크도 포함할 수 있습니다. 디스크는 VHD로 저장됩니다.
- VHD는 Azure의 Standard 또는 Premium Storage 계정에 페이지 Blob으로 저장됩니다.
    - Standard Storage: 대기 시간이 중요하지 않은 워크로드를 실행하는 VM에 대한 신뢰할 수 있는 저비용 디스크 지원입니다. Standard Storage는 표준 SSD 디스크나 표준 HDD 디스크를 사용할 수 있습니다.
    - Premium Storage: 고성능 디스크 지원입니다. 프리미엄 SSD 디스크를 사용합니다.
- 디스크에는 다음과 같은 여러 성능 계층이 있습니다.
    - 표준 HDD 디스크: HDD를 통해 지원되며 경제적인 스토리지에 사용됩니다.
    - 표준 SSD 디스크: 프리미엄 SSD 디스크와 표준 HDD 디스크의 요소를 결합하여 HDD보다 일관성 있는 성능과 안정성을 제공하지만 여전히 경제적입니다.
    - 프리미엄 SSD 디스크: SSD를 통해 지원되며 I/O를 많이 사용하는 워크로드를 실행하는 VM을 위해 고성능 및 낮은 대기 시간을 제공합니다.
- 다음과 같은 관리 또는 비관리 디스크가 있습니다.
    - 비관리 디스크: VM에서 사용되는 일반적인 유형의 디스크입니다. 이러한 디스크의 경우 사용자 고유의 스토리지 계정을 만든 다음, 디스크를 만들 때 지정합니다. VM의 스토리지 리소스를 최대화하는 방법을 파악해야 합니다.
    - 관리 디스크: Azure에서 자동으로 스토리지 계정 만들기 및 관리를 처리합니다. 디스크 크기와 성능 계층을 지정하면 Azure에서 디스크를 만들고 관리합니다. 디스크를 추가하고 VM의 크기를 조정하면 Azure에서 스토리지를 처리합니다.

자세한 정보:

- [Windows](../virtual-machines/windows/about-disks-and-vhds.md) 및 [Linux](../virtual-machines/linux/about-disks-and-vhds.md) VM의 디스크 스토리지에 대해 자세히 알아봅니다.
- [Standard](../virtual-machines/windows/standard-storage.md) 및 [Premium](../virtual-machines/windows/premium-storage.md) Storage에 대해 알아봅니다.


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Premium Storage를 사용하여 Azure VM 백업 및 복원 

Azure Backup과 함께 Premium Storage를 사용하여 Azure VM을 백업할 수 있습니다.

- Premium Storage를 사용하여 VM을 백업하는 동안, Backup 서비스는 Premium Storage 계정에 “AzureBackup-”이라는 임시 준비 위치를 만듭니다. 준비 위치의 크기는 복구 지점 스냅숏의 크기와 같습니다.
- Premium Storage 계정에 임시 준비 위치를 수용할 충분한 여유 공간이 있어야 합니다. [자세히 알아보기](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets). 준비 위치를 수정하지 마세요.
- 백업 작업이 완료되면 준비 위치가 삭제됩니다.
- 준비 위치에 사용되는 스토리지의 가격은 모든 [Premium Storage 가격 책정](../virtual-machines/windows/premium-storage.md#pricing-and-billing)과 일관성이 있습니다.

Premium Storage를 사용하여 Azure VM을 복원하는 경우 Premium 또는 Standard Storage로 복원할 수 있습니다. 일반적으로 Premium Storage로 복원하지만. VM의 일부 파일만 필요한 경우 Standard Storage로 복원하는 것이 경제적일 수 있습니다.

### <a name="backing-up-and-restoring-managed-disks"></a>관리 디스크 백업 및 복원

관리 디스크를 사용하여 Azure VM을 백업할 수 있습니다.
- 다른 Azure VM과 동일한 방식으로 관리 디스크를 사용하여 VM을 백업합니다. 가상 머신 설정에서 직접 VM을 백업하거나, Recovery Services 자격 증명 모음에서 VM에 대한 백업을 사용하도록 설정할 수 있습니다.
- Managed Disks에 VM 백업은 Managed Disks를 기반으로 구축된 RestorePoint 컬렉션을 통해 지원됩니다.
- Azure Backup은 ADE(Azure Disk Encryption)를 사용하여 암호화된 관리 디스크 VM의 백업도 지원합니다.

관리 디스크를 포함하는 VM을 복원하는 경우 관리 디스크를 포함하는 전체 VM으로 복원하거나 스토리지 계정으로 복원할 수 있습니다.
- 복원 프로세스 중에 Azure에서 관리 디스크를 처리하며, 스토리지 계정 옵션을 통해 사용자가 복원 중에 생성되는 스토리지 계정을 관리합니다.
- 암호화된 관리형 VM을 복원하는 경우 복원 프로세스를 시작하기 전에 VM 키와 비밀이 키 자격 증명 모음에 있어야 합니다.




## <a name="next-steps"></a>다음 단계

- 지원 매트릭스를 [검토](backup-support-matrix.md)하여 백업 시나리오에 대해 지원되는 기능 및 제한 사항을 알아봅니다.
- 다음 시나리오 중 하나에 대해 백업을 설정합니다.
    - [Azure VM 백업](backup-azure-arm-vms-prepare.md)
    - 백업 서버를 사용하지 않고 [Windows 머신을 직접 백업](tutorial-backup-windows-server-to-azure.md)합니다.
    - Azure에 백업하도록 [MABS를 설정](backup-azure-microsoft-azure-backup.md)한 다음, 워크로드를 MABS에 백업합니다.
    - Azure에 백업하도록 [DPM을 설정](backup-azure-dpm-introduction.md)한 다음, 워크로드를 DPM에 백업합니다.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

