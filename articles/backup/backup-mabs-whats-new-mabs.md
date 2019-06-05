---
title: Microsoft Azure Backup Server의 새로운 기능
description: Microsoft Azure Backup Server에서는 VM, 파일 및 폴더, 워크로드 등을 보호하기 위한 향상된 백업 기능을 제공합니다. Azure Backup Server V3을 설치하거나 이 버전으로 업그레이드하는 방법을 알아봅니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 5718064994a80266c216ae6040746be29194adc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254719"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Microsoft Azure Backup Server의 새로운 기능

Microsoft Azure Backup Server 버전 3(MABS V3)은 최신 업그레이드이며, 중요한 버그 수정, Windows Server 2019 지원, SQL 2017 지원 및 기타 기능 및 향상된 기능을 포함합니다. 수정된 버그 목록 및 MABS V3에 대한 설치 지침을 보려면 KB 문서 [4457852](https://support.microsoft.com/en-us/help/4457852/microsoft-azure-backup-server-v3)를 참조하세요.

다음 기능이 MABS V3에 포함됩니다.

## <a name="volume-to-volume-migration"></a>볼륨 간 마이그레이션
MABS V2에서 MBS(Modern Backup Storage)를 사용하여 워크로드 인식 스토리지를 발표했습니다. 여기에서 스토리지 속성에 따라 특정 스토리지로 백업할 특정 워크로드를 구성합니다. 그러나 구성 후 최적화된 리소스 사용률을 위해 특정 데이터 원본의 백업을 다른 스토리지로 이동해야 함을 알 수 있습니다. MABS V3은 백업을 마이그레이션하고 이를 [3단계](https://blogs.technet.microsoft.com/dpm/2017/10/24/storage-migration-with-dpm-2016-mbs/)로 다른 볼륨에 저장하도록 구성하는 기능을 제공합니다.

## <a name="prevent-unexpected-data-loss"></a>예기치 않은 데이터 손실 방지
기업에서 MABS는 관리자 팀에서 관리됩니다. 백업에 사용해야 하는 스토리지에 대한 지침이 있지만 백업 스토리지로 MABS에 지정된 올바르지 않은 볼륨은 중요한 데이터 손실을 발생시킬 수 있습니다. MABS V3과 함께 [이러한 PowerShell cmdlet](https://docs.microsoft.com/system-center/dpm/add-storage#volume-exclusion)을 사용하여 스토리지에 사용할 수 없는 것으로 해당 볼륨을 구성하여 이러한 시나리오를 방지할 수 있습니다.

## <a name="custom-size-allocation"></a>사용자 지정 크기 할당
MBS(Modern Backup Storage)는 필요한 경우 스토리지를 얇게 소비합니다. 이렇게 하기 위해 MABS는 보호를 위해 구성된 경우 백업 중인 데이터의 크기를 계산합니다. 그러나 파일 서버의 경우와 같이 많은 파일 및 폴더가 함께 백업되는 경우 크기 계산에 시간이 오래 걸릴 수 있습니다. MABS V3을 사용하여 각 파일의 크기를 계산하는 대신 기본값으로 볼륨 크기를 적용하도록 MABS를 구성하여 시간을 절약할 수 있습니다.

## <a name="optimized-cc-for-rct-vms"></a>RCT VM에 최적화된 참조
MABS는 VM이 충돌할 때 시간이 오래 걸리는 일관성 검사에 대한 필요성을 줄이는 RCT(Hyper-V의 기본 변경 내용 추적)를 사용합니다. RCT는 VSS 스냅샷 기반 백업에서 제공하는 변경 내용 추적보다 더 나은 복원력을 제공합니다. MABS V3은 모든 일관성 검사 중 변경된 데이터만을 전송하여 네트워크 및 스토리지 사용을 더욱 최적화합니다.

## <a name="support-to-tls-12"></a>TLS 1.2에 대한 지원
TLS 1.2는 최상의 암호화를 사용하여 Microsoft에서 제안하는 통신의 보안 방법입니다. MABS는 이제 인증서 기반 인증 및 클라우드 백업을 위해 MABS와 보호된 서버 사이의 TLS 1.2 통신을 지원합니다.

## <a name="vmware-vm-protection-support"></a>VMware VM 보호 지원
VMware VM 백업은 이제 프로덕션 배포에 지원됩니다. MABS V3은 VMware VM 보호를 위해 다음을 제공합니다.

-   5.5 및 6.0에 대한 지원과 함께 vCenter 및 ESXi 6.5에 대한 지원
- 클라우드로 VMware VM의 자동 보호 새 VMware VM이 보호된 폴더에 추가되는 경우 디스크 및 클라우드로 자동으로 보호됩니다.
- VMware 대체 위치 복구를 위한 복구 효율성 개선

## <a name="sql-2017-support"></a>SQL 2017 지원
MABS 데이터베이스로 SQL 2017과 함께 MABS V3을 설치할 수 있습니다. SQL 2016에서 SQL 2017로 SQL 서버를 업그레이드하거나 새로 설치할 수 있습니다. MABS V3을 사용하여 클러스터형 및 비클러스터형 환경에서 모두 SQL 2017 워크로드를 백업할 수도 있습니다.

## <a name="windows-server-2019-support"></a>Windows Server 2019 지원
Windows Server 2019에 MABS V3을 설치할 수 있습니다. WS2019와 함께 MABS V3을 사용하기 위해 MABS V3으로 설치/업그레이드하기 전에 OS를 WS2019로 업그레이드하거나 WS2016에서 V3 설치/업그레이드 후에 OS를 업그레이드할 수 있습니다.

MABS V3은 정식 릴리스이며, Windows Server 2016, Windows Server 2019에 직접 설치하거나 MABS V2에서 업그레이드할 수 있습니다. Backup Server V3으로 업그레이드하거나 이 버전을 설치하기 전에 설치 필수 구성 요소를 읽어 보세요.
[여기](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package)에서 MABS에 대한 설치/업그레이드 단계에 대한 자세한 내용을 참조하세요.


> [!NOTE]
> 
> MABS에는 System Center Data Protection Manager와 동일한 코드베이스가 있습니다. MABS v3은 Data Protection Manager 1807에 해당합니다.

## <a name="next-steps"></a>다음 단계

서버를 준비하는 방법을 알아보거나 워크로드 보호를 시작합니다.
- [MABS V3의 알려진 문제](backup-mabs-release-notes-v3.md)
- [Backup Server 워크로드 준비](backup-azure-microsoft-azure-backup.md)
- [Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
- [Backup Server를 사용하여 SQL Server 백업](backup-azure-sql-mabs.md)
- [Backup Server에서 Modern Backup Storage 사용](backup-mabs-add-storage.md)
