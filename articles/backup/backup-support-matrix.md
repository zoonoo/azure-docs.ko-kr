---
title: Azure Backup 지원 매트릭스
description: Azure Backup 서비스에 대한 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 99dd3c0b07307f2d0bf97dbff697e32e648705ae
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400169"
---
# <a name="azure-backup-support-matrix"></a>Azure Backup 지원 매트릭스

사용할 수 있습니다 [Azure Backup](backup-overview.md) Microsoft Azure 클라우드 플랫폼으로 데이터를 백업 합니다. 이 문서에서는 일반 지원 설정 및 Azure Backup 시나리오 및 배포에 대 한 제한 사항 요약입니다.

기타 지원 매트릭스를 사용할 수 있습니다.

- 에 대 한 지원 매트릭스 [Azure 가상 머신 (VM) 백업](backup-support-matrix-iaas.md)
- 사용 하 여 백업에 대 한 지원 매트릭스 [System Center Data Protection Manager (DPM) / Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- 사용 하 여 백업에 대 한 지원 매트릭스는 [Microsoft Azure Recovery Services (MARS) 에이전트](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>자격 증명 모음 지원

Azure Backup을 조율하 고 관리할 백업 Recovery Services 자격 증명 모음을 사용 합니다. 또한 백업 데이터를 저장할 자격 증명 모음을 사용 합니다.

다음 표에서 Recovery Services 자격 증명의 기능을 설명 합니다.

**기능** | **세부 정보**
--- | ---
**구독의 자격 증명 모음 수** | 단일 구독에서 최대 500개의 Recovery Services 자격 증명 모음입니다.
**자격 증명 모음의 머신 수** | 단일 자격 증명 모음에서 최대 1,000 개의 Azure Vm입니다.<br/><br/> 최대 50대의 MABS 서버를 단일 자격 증명 모음에 등록할 수 있습니다.
**자격 증명 모음 스토리지의 데이터 원본 수** | 최대 54,400GB 합니다. Azure VM 백업에는 제한이 없습니다.
**자격 증명 모음에 대한 백업 횟수** | **Azure Vm의 경우:** 하루에 한 번입니다.<br/><br/>**DPM/MABS에서 보호 되는 컴퓨터:** 하루에 두 번입니다.<br/><br/> **MARS 에이전트를 사용 하 여 직접 백업 하는 컴퓨터:** 하루에 3 번입니다.
**자격 증명 모음 간 백업** | 백업은 한 지역 내에서 수행됩니다.<br/><br/> 백업할 VM이 포함된 각 Azure 지역에 자격 증명 모음이 있어야 합니다. 다른 지역으로 백업할 수 없습니다.
**자격 증명 모음 이동** | 할 수 있습니다 [자격 증명 모음 이동](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) 구독의 간이나 동일한 구독에 리소스 그룹입니다.
**자격 증명 모음 간 데이터 이동** | 자격 증명 모음 간에 백업 데이터 이동이 지원 되지 않습니다.
**자격 증명 모음 스토리지 유형 수정** | 전에 백업이 저장 되는 자격 증명 모음에 대 한 저장소 복제 유형 (지역 중복 저장소 또는 로컬 중복 저장소)를 수정할 수 있습니다. 자격 증명 모음에서 백업이 시작되면 복제 유형을 수정할 수 없습니다.

## <a name="on-premises-backup-support"></a>온-프레미스 백업 지원

온-프레미스 컴퓨터를 백업 하려면 지원 되는 다음과 같습니다.

**머신** | **백업 항목** | **위치**: | **기능**
--- | --- | --- | ---
**MARS 에이전트를 사용한 Windows 머신 직접 백업** | 파일, 폴더, 시스템 상태 | Recovery Services 자격 증명 모음에 백업 합니다. | 하루에 3 번 백업<br/><br/> 응용 프로그램 인식 백업 없음<br/><br/> 파일, 폴더, 볼륨 복원
**MARS 에이전트를 사용한 Linux 머신 직접 백업** | 백업이 지원 되지 않음
**DPM 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | 로컬 DPM 스토리지에 백업됩니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대 한 전체 세분성<br/><br/> Linux Vm (Hyper-v/VMware)에 대 한 지원<br/><br/> Oracle 지원 되지 않습니다
**MABS에 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS 로컬 스토리지에 백업됩니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대 한 전체 세분성<br/><br/> Linux Vm (Hyper-v/VMware)에 대 한 지원<br/><br/> Oracle 지원 되지 않습니다

## <a name="azure-vm-backup-support"></a>Azure VM 백업 지원

### <a name="azure-vm-limits"></a>Azure VM 제한

**제한** | **세부 정보**
--- | ---
**Azure VM 데이터 디스크** | 16 개로 제한
**Azure VM 데이터 디스크 크기** | 개별 디스크에 최대 4,095GB 수 합니다.

### <a name="azure-vm-backup-options"></a>Azure VM 백업 옵션

Azure Vm 백업 하려는 경우 지원 되는 다음과 같습니다.

**머신** | **백업 항목** | **위치**: | **기능**
--- | --- | --- | ---
**VM 확장을 사용 하 여 azure VM 백업** | 전체 VM | 자격 증명 모음에 백업 합니다. | VM에 백업을 사용하도록 설정할 때 설치된 확장<br/><br/> 하루에 한 번 백업 합니다.<br/><br/> Windows Vm에 대 한 응용 프로그램 인식 백업 Linux Vm의 파일 일치 백업 합니다. 사용자 지정 스크립트를 사용 하 여 Linux 컴퓨터에 대 한 앱 일관성을 구성할 수 있습니다.<br/><br/> VM 또는 디스크를 복원 합니다.<br/><br/> 온-프레미스 위치에 VM을 Azure에 백업할 수 없습니다.
**MARS 에이전트를 사용 하 여 azure VM 백업** | 파일, 폴더, 시스템 상태 | 자격 증명 모음에 백업 합니다. | 하루에 3 번을 백업 합니다.<br/><br/> 전체 VM 대신 특정 파일 또는 폴더를 백업 하려는 경우 MARS 에이전트 VM 확장을 함께 실행할 수 있습니다.
**Azure VM(DPM 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | DPM을 실행 하는 Azure VM의 로컬 저장소에 백업 합니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏입니다.<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음
**Azure VM(MABS 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS를 실행 하는 Azure VM의 로컬 저장소에 백업 합니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏입니다.<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음

## <a name="linux-backup-support"></a>Linux 백업 지원

Linux 컴퓨터를 백업 하려면 지원 되는 다음과 같습니다.

**백업 유형** | **Linux(Azure 인증)**
--- | ---
**Linux를 실행 하는 온-프레미스 컴퓨터의 직접 백업** | 지원되지 않습니다. MARS 에이전트는 Windows 컴퓨터에만 설치할 수 있습니다.
**에이전트 확장을 사용 하 여 Linux를 실행 하는 Azure VM을 백업 하려면** | 사용 하 여 앱 일치 백업 [사용자 지정 스크립트](backup-azure-linux-app-consistent.md)합니다.<br/><br/> 파일 수준 복구입니다.<br/><br/> 복구 지점 또는 디스크에서 VM을 만들어 복원합니다.
**DPM을 사용 하 여 온-프레미스 나 Linux를 실행 하는 Azure VM 백업** | Hyper-v 및 VMWare Linux 게스트 Vm의 파일 일치 백업 합니다.<br/><br/> Hyper-v 및 VMWare Linux 게스트 Vm의 VM 복원 합니다.<br/><br/> 파일 일치 백업만 Azure VM에 사용할 수 없습니다.
**MABS를 사용 하 여 온-프레미스 컴퓨터 또는 Linux를 실행 하는 Azure VM 백업** | Hyper-v 및 VMWare Linux 게스트 Vm의 파일 일치 백업 합니다.<br/><br/> Hyper-v 및 VMWare Linux 게스트 Vm의 VM 복원 합니다.<br/><br/> Azure VM에서 파일 일치 백업을 사용할 수 없습니다.

## <a name="daylight-saving-time-support"></a>일광 절약 시간 지원

Azure Backup은 Azure VM 백업에 대 한 일광 절약 시간에 대 한 자동 클록 조정을 지원 하지 않습니다. 필요에 따라 수동으로 백업 정책을 수정합니다.

## <a name="disk-deduplication-support"></a>디스크 중복 제거 지원

디스크 중복 제거 지원은 다음과 같습니다.

- 디스크 중복 제거는 지원 되는 온-프레미스를 사용 하 여 DPM 또는 MABs Windows를 실행 하는 Hyper-v Vm을 백업 합니다. Windows Server 백업 저장소로 VM에 연결 된 가상 하드 디스크 (Vhd)에 호스트 수준에서 데이터 중복 제거를 수행 합니다.
- Azure에서는 백업 구성 요소에 대한 중복 제거가 지원되지 않습니다. DPM 및 MABS를 Azure에 배포한 VM에 연결 된 저장소 디스크 중복 제거 수 없습니다.

## <a name="security-and-encryption-support"></a>보안 및 암호화 지원

Azure Backup은 전송 중 및 미사용 데이터에 대 한 암호화를 지원 합니다.

### <a name="network-traffic-to-azure"></a>Azure 네트워크 트래픽

- Recovery Services 자격 증명 모음에 서버에서 백업 트래픽은 고급 암호화 표준 256을 사용 하 여 암호화 됩니다.
- 백업 데이터는 보안 HTTPS 링크를 통해 전송됩니다.
- 백업 데이터는 암호화 된 형태로 Recovery Services 자격 증명 모음에 저장 됩니다.
- 사용자만 이 데이터의 잠금을 해제할 수 있는 암호를 보유합니다. Microsoft는 어떠한 경우에도 백업 데이터의 암호를 해독할 수 없습니다.

    > [!WARNING]
    > 자격 증명 모음이 설정되면 암호화 키에 대한 액세스 권한만 부여됩니다. Microsoft는 복사본을 유지 관리할 수 없으며, 키에 대한 액세스 권한도 없습니다. 키를 잃어버리면 Microsoft에서 백업 데이터를 복구할 수 없습니다.

### <a name="data-security"></a>데이터 보안

- 암호화를 설정 해야 하는 Azure Vm을 백업 하는 경우 *내에서* 가상 컴퓨터.
- Azure Backup은 Azure Disk Encryption을 지원하며, Windows 가상 머신의 BitLocker와 Linux 가상 머신의 **dm-crypt**를 사용합니다.
- Azure Backup 사용 하 여 백 엔드에서 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md), 미사용 데이터를 보호 하는 합니다.

**머신** | **전송 중** | **저장**
--- | --- | ---
**DPM/MABS 없이 온-프레미스 Windows 컴퓨터** | ![예][green] | ![예][green]
**Azure VM** | ![예][green] | ![예][green]
**DPM 사용 하 여 Azure Vm 또는 온-프레미스 Windows 컴퓨터** | ![예][green] | ![예][green]
**MABS 사용 하 여 Azure Vm 또는 온-프레미스 Windows 컴퓨터** | ![예][green] | ![예][green]

## <a name="compression-support"></a>압축 지원

다음 표에 요약 된 것 처럼 backup에서 백업 트래픽을 압축을 지원 합니다.

- Azure Vm에 대 한 VM 확장을 읽으므로 데이터를 Azure storage 계정에서 직접 저장소 네트워크를 통해이 트래픽을 압축할 필요가 없습니다.
- DPM 또는 MABS를 사용 하는 경우 백업 하기 전에 데이터를 압축 하 여 대역폭을 줄일 수 있습니다.

**머신** | **MABS/DPM에 압축(TCP)** | **자격 증명 모음 (HTTPS)에 압축**
--- | --- | ---
**온-프레미스 Windows 머신 직접 백업** | 해당 없음 | ![예][green]
**VM 확장을 사용 하 여 Azure Vm의 백업** | 해당 없음 | 해당 없음
**MABS/DPM을 사용 하 여 Azure 온-프레미스/컴퓨터에 백업** | ![예][green] | ![예][green]

## <a name="retention-limits"></a>보존 제한

**설정** | **제한**
--- | ---
**(컴퓨터 또는 작업)는 보호 된 인스턴스당 최대 복구 지점** | 9,999
**복구 지점에 대 한 최대 만료 시간** | 제한 없음
**DPM/MABS에 최대 백업 빈도** | SQL Server에 대해 15분마다<br/><br/> 다른 워크 로드에 대 한 시간에 한 번
**자격 증명 모음에 최대 백업 빈도** | **온-프레미스 Windows 컴퓨터 또는 MARS를 실행 하는 Azure Vm:** 하루 3<br/><br/> **DPM/MABS:** 하루에 두 번<br/><br/> **Azure VM 백업의 경우:** 하루 하나
**복구 지점 보존** | 매일, 매주, 매월, 매년
**최대 보존 기간** | 백업 빈도에 따라 다름
**DPM/MABS 디스크에 복구 지점** | 파일 서버에 대 한 64 응용 프로그램 서버의 경우 448 <br/><br/>온-프레미스 DPM에 대 한 무제한 테이프 복구 지점

## <a name="next-steps"></a>다음 단계

- Azure VM 백업에 대한 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
