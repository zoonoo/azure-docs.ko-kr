---
title: Azure Backup 지원 매트릭스
description: Azure Backup 서비스에 대한 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: d036e527880a98d323e8de2f3a8721d7e12dbb07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273268"
---
# <a name="support-matrix-for-azure-backup"></a>Azure 백업을 위한 지원 매트릭스

[Azure 백업을](backup-overview.md) 사용하여 Microsoft Azure 클라우드 플랫폼에 데이터를 백업할 수 있습니다. 이 문서에서는 Azure Backup 시나리오 및 배포에 대한 일반적인 지원 설정 및 제한 사항에 대해 간략히 요약합니다.

기타 지원 매트릭스를 사용할 수 있습니다.

- [Azure 가상 시스템(VM) 백업에](backup-support-matrix-iaas.md) 대한 지원 매트릭스
- 시스템 센터 데이터 [보호 관리자(DPM)/Microsoft Azure 백업 서버(MABS)를](backup-support-matrix-mabs-dpm.md) 사용하여 백업을 위한 지원 매트릭스
- [Microsoft Azure 복구 서비스(MARS) 에이전트를](backup-support-matrix-mars-agent.md) 사용하여 백업을 위한 지원 매트릭스

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>자격 증명 모음 지원

Azure Backup은 복구 서비스 자격 증명 모음을 사용하여 백업을 오케스트레이션하고 관리합니다. 또한 백업된 데이터를 저장하기 위해 볼트를 사용합니다.

다음 표에서는 복구 서비스 자격 증명 모음의 기능에 대해 설명합니다.

**기능** | **세부 정보**
--- | ---
**구독의 자격 증명 모음 수** | 단일 구독에서 최대 500개의 Recovery Services 자격 증명 모음입니다.
**자격 증명 모음의 머신 수** | 단일 볼트에서 최대 1,000개의 Azure VM을 볼 수 있습니다.<br/><br/> 최대 50대의 MABS 서버를 단일 자격 증명 모음에 등록할 수 있습니다.
**데이터 원본** | 개별 [데이터 원본의](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#how-is-the-data-source-size-determined) 최대 크기는 54,400GB입니다. 이 제한은 Azure VM 백업에는 적용되지 않습니다. 볼트에 백업할 수 있는 총 데이터 양에는 제한이 적용되지 않습니다.
**자격 증명 모음에 대한 백업 횟수** | **Azure VM:** 하루에 한 번.<br/><br/>**DPM/MABS로 보호되는 기계:** 하루에 두 번.<br/><br/> **MARS 에이전트를 사용하여 직접 백업되는 컴퓨터:** 하루에 세 번.
**자격 증명 모음 간 백업** | 백업은 한 지역 내에서 수행됩니다.<br/><br/> 백업할 VM이 포함된 각 Azure 지역에 자격 증명 모음이 있어야 합니다. 다른 지역으로 백업할 수 없습니다.
**볼트 이동** | 구독 간에 또는 동일한 구독의 리소스 그룹 간에 [자격 증명 모음을 이동할](https://docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) 수 있습니다. 그러나 여러 리전으로 볼트를 이동하는 것은 지원되지 않습니다.
**자격 증명 모음 간의 데이터 이동** | 백업된 데이터를 볼트 간에 이동하는 것은 지원되지 않습니다.
**자격 증명 모음 스토리지 유형 수정** | 백업을 저장하기 전에 볼트에 대한 저장소 복제 유형(지역 중복 저장소 또는 로컬 중복 저장소)을 수정할 수 있습니다. 자격 증명 모음에서 백업이 시작되면 복제 유형을 수정할 수 없습니다.

## <a name="on-premises-backup-support"></a>온-프레미스 백업 지원

온-프레미스 컴퓨터를 백업하려는 경우 지원되는 내용은 다음과 같습니다.

**컴퓨터** | **백업되는 내용** | **위치** | **기능**
--- | --- | --- | ---
**MARS 에이전트를 사용한 Windows 머신 직접 백업** | 파일, 폴더, 시스템 상태 | 복구 서비스 자격 증명 모음에 백업합니다. | 하루에 세 번 백업<br/><br/> 앱 인식 백업 없음<br/><br/> 파일, 폴더, 볼륨 복원
**MARS 에이전트를 사용한 Linux 머신 직접 백업** | 백업이 지원되지 않음
**DPM에 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | 로컬 DPM 스토리지에 백업합니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구를 위한 전체 세분성<br/><br/> VM(하이퍼 V/VM웨어)용 으로 지원되는 리눅스<br/><br/> 오라클이 지원되지 않음
**MABS에 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS 로컬 스토리지에 백업합니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구를 위한 전체 세분성<br/><br/> VM(하이퍼 V/VM웨어)용 으로 지원되는 리눅스<br/><br/> 오라클이 지원되지 않음

## <a name="azure-vm-backup-support"></a>Azure VM 백업 지원

### <a name="azure-vm-limits"></a>Azure VM 제한

**제한** | **세부 정보**
--- | ---
**Azure VM 데이터 디스크 수** | 제한 16 <br> 16개 이상의 디스크(최대 32개 디스크)를 사용하여 VM의 프라이빗 미리 보기에 등록하려면 AskAzureBackupTeam@microsoft.com으로 이메일을 보내주세요.
**Azure VM 데이터 디스크 크기** | 개별 디스크 크기는 VM의 모든 디스크에 대해 최대 32TB, 최대 256TB까지 결합될 수 있습니다.

### <a name="azure-vm-backup-options"></a>Azure VM 백업 옵션

Azure VM을 백업하려는 경우 지원되는 내용은 다음과 같습니다.

**컴퓨터** | **백업되는 내용** | **위치** | **기능**
--- | --- | --- | ---
**VM 확장을 사용하여 Azure VM 백업** | 전체 VM | 볼트에 백업합니다. | VM에 백업을 사용하도록 설정할 때 설치된 확장<br/><br/> 하루에 한 번 백업하십시오.<br/><br/> Windows VM용 앱 인식 백업; 리눅스 VM에 대한 파일 일관된 백업. 사용자 지정 스크립트를 사용하여 Linux 컴퓨터에 대한 앱 일관성을 구성할 수 있습니다.<br/><br/> VM 또는 디스크를 복원합니다.<br/><br/> Azure VM을 온-프레미스 위치에 백업할 수 없습니다.
**MARS 에이전트를 사용하여 Azure VM 백업** | 파일, 폴더, 시스템 상태 | 볼트에 백업합니다. | 하루에 세 번 백업하십시오.<br/><br/> 전체 VM이 아닌 특정 파일이나 폴더를 백업하려는 경우 MARS 에이전트는 VM 확장명과 함께 실행할 수 있습니다.
**Azure VM(DPM 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | DPM을 실행하는 Azure VM의 로컬 저장소에 백업합니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷입니다.<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음
**Azure VM(MABS 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS를 실행하는 Azure VM의 로컬 저장소에 백업합니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷입니다.<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음

## <a name="linux-backup-support"></a>Linux 백업 지원

Linux 컴퓨터를 백업하려는 경우 지원되는 내용은 다음과 같습니다.

**백업 유형** | **Linux(Azure 인증)**
--- | ---
**Linux를 실행하는 온-프레미스 시스템의 직접 백업** | 지원되지 않습니다. MARS 에이전트는 Windows 컴퓨터에만 설치할 수 있습니다.
**에이전트 확장을 사용하여 Linux를 실행하는 Azure VM을 백업합니다.** | [사용자 지정 스크립트를](backup-azure-linux-app-consistent.md)사용하여 앱 일관된 백업.<br/><br/> 파일 수준 복구입니다.<br/><br/> 복구 지점 또는 디스크에서 VM을 만들어 복원합니다.
**DPM을 사용하여 Linux를 실행하는 온-프레미스 컴퓨터를 백업합니다.** | 하이퍼 V 및 VMWare에서 리눅스 게스트 VM의 파일 일관된 백업.<br/><br/> 하이퍼 V 및 VM웨어 리눅스 게스트 VM의 VM 복원.
**MABS를 사용하여 Linux를 실행하는 온-프레미스 컴퓨터를 백업합니다.** | 하이퍼 V 및 VMWare에서 리눅스 게스트 VM의 파일 일관된 백업.<br/><br/> 하이퍼 V 및 VM웨어 리눅스 게스트 VM의 VM 복원.
**MABS 또는 DPM을 사용하여 Linux Azure VM백업** | 지원되지 않습니다.

## <a name="daylight-saving-time-support"></a>일광 절약 시간제 지원

Azure BackupAzure VM 백업에 대 한 일광 절약 시간에 대 한 자동 클럭 조정을 지원 하지 않습니다. 백업 시간을 앞뒤로 이동하지 않습니다. 백업이 원하는 시간에 실행되도록 하려면 필요에 따라 백업 정책을 수동으로 수정합니다.

## <a name="disk-deduplication-support"></a>디스크 중복 제거 지원

디스크 중복 제거 지원은 다음과 같습니다.

- DPM 또는 MAB를 사용하여 Windows를 실행하는 Hyper-VM을 백업할 때 디스크 중복 제거가 온-프레미스에서 지원됩니다. Windows Server는 VM에 백업 저장소로 연결된 VH(가상 하드 디스크)에서 호스트 수준에서 데이터 중복 제거를 수행합니다.
- Azure에서는 백업 구성 요소에 대한 중복 제거가 지원되지 않습니다. Azure에 DPM 및 MABS를 배포하면 VM에 연결된 저장소 디스크를 중복 제거할 수 없습니다.

## <a name="security-and-encryption-support"></a>보안 및 암호화 지원

Azure Backup은 전송 중 및 미사용 데이터에 대한 암호화를 지원합니다.

### <a name="network-traffic-to-azure"></a>Azure로의 네트워크 트래픽

- 서버에서 복구 서비스 자격 증명 모음으로의 백업 트래픽은 고급 암호화 표준 256을 사용하여 암호화됩니다.
- 백업 데이터는 보안 HTTPS 링크를 통해 전송됩니다.
- 백업 데이터는 복구 서비스 자격 증명 모음에 암호화된 형식으로 저장됩니다.
- 사용자만 이 데이터의 잠금을 해제할 수 있는 암호를 보유합니다. Microsoft는 어떠한 경우에도 백업 데이터의 암호를 해독할 수 없습니다.

    > [!WARNING]
    > 자격 증명 모음이 설정되면 암호화 키에 대한 액세스 권한만 부여됩니다. Microsoft는 복사본을 유지 관리할 수 없으며, 키에 대한 액세스 권한도 없습니다. 키를 잃어버리면 Microsoft에서 백업 데이터를 복구할 수 없습니다.

### <a name="data-security"></a>데이터 보안

- Azure VM을 백업할 때는 가상 시스템 *내에서* 암호화를 설정해야 합니다.
- Azure Backup은 Azure Disk Encryption을 지원하며, Windows 가상 머신의 BitLocker와 Linux 가상 머신의 **dm-crypt**를 사용합니다.
- 백 엔드에서 Azure Backup은 미사용 데이터를 보호하는 [Azure 저장소 서비스 암호화를](../storage/common/storage-service-encryption.md)사용합니다.

**컴퓨터** | **전송 중** | **미사용**
--- | --- | ---
**온-프레미스 Windows 머신(DPM/MABS 사용 안 함)** | ![yes][green] | ![yes][green]
**Azure VM** | ![yes][green] | ![yes][green]
**온-프레미스 Windows 컴퓨터 또는 DPM이 있는 Azure VM** | ![yes][green] | ![yes][green]
**온-프레미스 Windows 컴퓨터 또는 MABS가 있는 Azure VM** | ![yes][green] | ![yes][green]

## <a name="compression-support"></a>압축 지원

백업은 다음 표에 요약된 백업 트래픽 압축을 지원합니다.

- Azure VM의 경우 VM 확장은 저장소 네트워크를 통해 Azure 저장소 계정에서 직접 데이터를 읽므로 이 트래픽을 압축할 필요가 없습니다.
- DPM 또는 MABS를 사용하는 경우 백업하기 전에 데이터를 압축하여 대역폭을 절약할 수 있습니다.

**컴퓨터** | **MABS/DPM에 압축(TCP)** | **볼트로 압축(HTTPS)**
--- | --- | ---
**온-프레미스 Windows 머신의 직접 백업** | 해당 없음 | ![yes][green]
**VM 확장을 사용하여 Azure VM 백업** | 해당 없음 | 해당 없음
**MABS/DPM을 사용하여 온-프레미스/Azure 컴퓨터에서 백업** | ![yes][green] | ![yes][green]

## <a name="retention-limits"></a>보존 제한

**설정** | **제한**
--- | ---
**보호된 인스턴스당 최대 복구 포인트(기계 또는 워크로드)** | 9,999
**복구 지점에 대한 최대 만료 시간** | 제한 없음
**DPM/MABS에 대한 최대 백업 빈도** | SQL Server에 대해 15분마다<br/><br/> 다른 워크로드에 대해 한 시간에 한 번
**자격 증명 모음에 대한 최대 백업 빈도** | **온-프레미스 Windows 컴퓨터 또는 MARS를 실행 하는 Azure VM:** 하루에 세 번<br/><br/> **DPM/MABS:** 1일 2회<br/><br/> **Azure VM 백업:** 1일 1회
**복구 지점 보존** | 매일, 매주, 매월, 매년
**최대 보존 기간** | 백업 빈도에 따라 다름
**DPM/MABS 디스크의 복구 지점 수** | 파일 서버의 경우 64; 앱 서버용 448 <br/><br/>온-프레미스 DPM을 위한 무제한 테이프 복구 포인트

## <a name="cross-region-restore"></a>교차 지역 복원

Azure Backup은 데이터 가용성 및 복원기능을 강화하기 위해 교차 지역 복원 기능을 추가하여 고객이 보조 지역으로 데이터를 복원할 수 있는 모든 권한을 부여했습니다. 이 기능을 구성하려면 [교차 지역 복원 설정 문서를 방문하십시오.](backup-create-rs-vault.md#set-cross-region-restore) 이 기능은 다음 관리 유형에 대해 지원됩니다.

| 백업 관리 유형 | 지원됨                                                    | 지원되는 지역 |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | 예. 4TB 디스크 미만의 암호화된 VM 및 VM에 대해 지원되는 공개 제한 미리 보기 | 미국 중서부   |
| 화성 에이전트/온프레미스 | 예                                                           | 해당 없음               |
| SQL /SAP 하나          | 예                                                           | 해당 없음               |
| Afs                    | 예                                                           | 해당 없음               |

## <a name="next-steps"></a>다음 단계

- Azure VM 백업에 대한 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
