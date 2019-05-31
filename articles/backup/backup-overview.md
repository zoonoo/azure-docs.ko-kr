---
title: Azure Backup이란?
description: Azure Backup 서비스에 대해 간략히 설명하고, BCDR(비즈니스 연속성 및 재해 복구) 전략에 어떤 방식으로 기여하는지 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bd90d315fd5590a8bd862a1a3397cf8c254fccc8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714291"
---
# <a name="what-is-azure-backup"></a>Azure Backup이란?

Azure Backup 서비스는 Microsoft Azure 클라우드에 데이터를 백업합니다. 온-프레미스 머신 및 워크로드와 Azure VM(가상 머신)을 백업할 수 있습니다.


## <a name="why-use-azure-backup"></a>Azure Backup을 사용하는 이유

Azure Backup에서 제공하는 주요 이점은 다음과 같습니다.

- **온-프레미스 백업 오프로드**: Azure Backup은 온-프레미스 리소스를 클라우드에 백업할 수 있는 간단한 솔루션을 제공합니다. 복잡한 온-프레미스 백업 솔루션을 배포할 필요 없이 장단기 백업을 수행합니다.
- **Azure IaaS VM 백업**: Azure Backup은 실수로 데이터가 삭제되지 않도록 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 백업은 복구 지점이 기본적으로 관리되는 Recovery Services 자격 증명 모음에 저장됩니다. 구성 및 확장성이 간단하고, 백업이 최적화되어 있으며, 필요에 따라 쉽게 복원할 수 있습니다.
- **손쉬운 크기 조정** - Azure Backup은 유지 관리 또는 모니터링 오버헤드 없이 기본 성능과 Azure 클라우드의 무제한 크기 조정을 사용하여 고가용성을 제공합니다.
- **무제한 데이터 전송**: Azure Backup은 사용자가 전송하는 인바운드 또는 아웃바운드 데이터의 양을 제한하거나, 전송되는 데이터에 대해 요금을 부과하지 않습니다.
    - 아웃바운드 데이터는 복원 작업 중에 Recovery Services 자격 증명 모음에서 전송된 데이터입니다.
    - Azure Import/Export 서비스를 통해 오프라인 초기 백업을 수행하여 대량의 데이터를 가져오는 경우 인바운드 데이터와 관련된 비용이 발생합니다.  [자세히 알아보기](backup-azure-backup-import-export.md).
- **안전한 데이터 유지**: Azure Backup은 전송 중 및 저장 데이터를 보호하기 위한 솔루션을 제공합니다.
- **애플리케이션 일치 백업 가져오기**: 애플리케이션 일치 백업은 백업 복사본을 복원하는 데 필요한 모든 데이터가 복구 지점에 있음을 의미합니다. Azure Backup에서 애플리케이션 일치 백업을 제공하므로 데이터를 복원하기 위한 추가 수정 프로그램이 필요하지 않습니다. 애플리케이션 일치 데이터를 복원하면 실행 상태로 빠르게 돌아갈 수 있으므로 복원 시간을 줄여줍니다.
- **단기 및 장기 데이터 보존**: 단기 및 장기 데이터 보존을 위해 Recovery Services 자격 증명 모음을 사용할 수 있습니다. Azure는 Recovery Services 자격 증명 모음에 데이터를 유지할 수 있는 기간을 제한하지 않습니다. 원하는 만큼 데이터를 유지할 수 있습니다. Azure Backup에는 보호된 인스턴스당 9999개 복구 지점의 제한이 있습니다. 이 제한이 백업 요구 사항에 미치는 영향에 대한 [자세한 정보](backup-introduction-to-azure-backup.md#backup-and-retention)를 확인하세요.
- **자동 저장소 관리** - 일부는 온-프레미스, 일부는 클라우드인 하이브리드 환경에는 종종 다른 유형의 저장소가 필요합니다. Azure Backup을 사용하면 온-프레미스 저장소 디바이스를 사용하기 위한 비용이 들지 않습니다. Azure Backup은 백업 스토리지를 자동으로 할당하고 관리하며, 종량제 모델을 사용하므로 사용한 스토리지 비용만 지불하면 됩니다. 가격 책정에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/backup).
- **여러 스토리지 옵션** - Azure Backup은 스토리지/데이터의 고가용성을 유지하는 두 가지 종류의 복제를 제공합니다.
    - [LRS(로컬 중복 스토리지)](../storage/common/storage-redundancy-lrs.md)는 데이터 센터의 스토리지 배율 단위로 데이터를 세 번 복제합니다(세 개의 데이터 복사본 생성). 모든 데이터 복사본은 동일한 지역 내에 있습니다. LRS는 로컬 하드웨어 오류로부터 데이터를 보호하기 위한 저비용 옵션입니다.
    - [GRS(지역 중복 스토리지)](../storage/common/storage-redundancy-grs.md)는 기본값이며 권장하는 복제 옵션입니다. GRS는 데이터 원본의 기본 위치에서 수백 마일 떨어진 보조 지역으로 데이터를 복제합니다. GRS는 LRS보다 많은 비용이 소요되지만 GRS는 지역 가동 중단이 발생해도 높은 수준의 데이터 내구성을 제공합니다.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure Backup과 Azure Site Recovery의 차이점은 무엇인가요?

Azure Backup 및 Azure Site Recovery 서비스는 모두 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. BCDR은 크게 다음 두 가지 목표로 구성됩니다.

- 중단이 발생하는 경우 비즈니스 데이터를 안전하게 유지하고 복구합니다.
- 계획되거나 계획되지 않은 가동 중지 동안 앱과 워크로드를 계속 가동하고 실행합니다.

두 서비스는 모두 보완적이지만 서로 다른 기능을 제공합니다.

- **Azure Site Recovery**: Site Recovery는 온-프레미스 머신 및 Azure VM에 대한 재해 복구 솔루션을 제공합니다. 머신을 주 위치에서 보조 위치로 복제합니다. 재해가 발생하면 머신을 보조 위치로 장애 조치하고 여기에서 액세스합니다. 모든 것이 정상적으로 다시 가동되고 실행되면 머신을 장애 복구하여 주 사이트에 복구합니다.
- **Azure Backup**: Azure Backup 서비스는 온-프레미스 머신 및 Azure VM에서 데이터를 백업합니다. 파일, 폴더, 머신 시스템 상태 및 앱 인식 데이터 백업을 포함한 세분화된 수준에서 데이터를 백업 및 복구할 수 있습니다. Azure Backup은 Site Recovery보다 더 세분화된 수준에서 데이터를 처리합니다. 예를 들어 랩톱의 프레젠테이션이 손상된 경우 Azure Backup을 사용하여 해당 프레젠테이션을 복원할 수 있습니다. VM 구성 및 데이터를 안전하게 유지하고 액세스할 수 있도록 하려면 Site Recovery를 사용할 수 있습니다.  

다음 표의 항목을 사용하여 BCDR 요구 사항을 파악합니다.

**목표** | **세부 정보** | **비교**
--- | --- | ---
**데이터 백업/보존** | 규정 준수 관점에서 필요한 경우 백업 데이터를 며칠, 몇 개월 또는 심지어 몇 년 동안 유지하고 저장할 수 있습니다. | Azure Backup과 같은 백업 솔루션을 사용하면 백업하려는 데이터를 세밀하게 선택하고 백업 및 보존 정책을 미세하게 튜닝할 수 있습니다.<br/><br/> Site Recovery는 이와 같은 미세 튜닝을 허용하지 않습니다.
**RPO(복구 지점 목표)** | 복구 작업을 수행해야 할 때 용인되는 데이터 손실의 양입니다. | 백업에는 더 가변적인 RPO가 있습니다.<br/><br/> 일반적으로 VM 백업은 1일의 RPO를 가지며 데이터베이스 백업은 15분 정도의 RPO를 갖습니다.<br/><br/> 복제가 연속적이거나 빈번하므로 Site Recovery에서는 낮은 RPO를 제공합니다. 이로 인해 원본과 복제본 복사본 간의 델타가 작습니다.
**RTO(복구 시간 목표)** |복구 또는 복원을 완료하는 데 걸리는 시간의 양입니다. | 더 큰 RPO로 인해 백업 솔루션이 처리해야 하는 데이터의 양이 일반적으로 훨씬 더 많고 결과적으로 RTO가 더 길어집니다. 예를 들어 테이프에서 데이터를 복원하면 오프사이트 위치에서 테이프를 전송하는 데 걸리는 시간에 따라 몇 일이 걸릴 수 있습니다.

## <a name="what-backup-scenarios-are-supported"></a>지원되는 백업 시나리오는 무엇인가요?

Azure Backup은 온-프레미스 머신과 Azure VM을 모두 백업할 수 있습니다.

**머신** | **백업 시나리오**
--- | ---
**온-프레미스 백업** |  1) 온-프레미스 Windows 머신에서 Azure Backup MARS(Microsoft Azure Recovery Services) 에이전트를 실행하여 개별 파일 및 시스템 상태를 백업합니다. <br/><br/>2) 온-프레미스 머신을 백업 서버(System Center DPM(Data Protection Manager) 또는 MABS(Microsoft Azure Backup Server))에 백업한 다음, Azure의 Azure Backup Recovery Services 자격 증명 모음에 백업하도록 백업 서버를 구성합니다.
**Azure VM** | 1) 개별 Azure VM에 백업을 사용하도록 설정합니다. 백업을 사용하도록 설정하면 Azure Backup은 VM에서 실행되는 Azure VM 에이전트의 확장을 설치합니다. 이 에이전트는 전체 VM을 백업합니다.<br/><br/> 2) Azure VM에서 MARS 에이전트를 실행합니다. VM의 개별 파일 및 폴더를 백업하려는 경우에 유용합니다.<br/><br/> 3) Azure에서 실행되는 DPM 서버 또는 MABS에 Azure VM을 백업합니다. 그런 다음, Azure Backup을 사용하는 자격 증명 모음에 DPM 서버/MABS를 백업합니다.


## <a name="why-use-a-backup-server"></a>백업 서버를 사용하는 이유는 무엇인가요?
머신과 앱을 MABS/DPM 스토리지에 백업한 다음, DPM/MABS 스토리지를 자격 증명 모음에 백업하면 다음과 같은 장점이 있습니다.

- MABS/DPM에 백업하면 파일/폴더/볼륨 백업 및 머신 상태 백업(완전 백업, 시스템 상태) 외에도 SQL Server, Exchange 및 SharePoint와 같은 일반적인 앱에 최적화된 앱 인식 백업을 제공합니다.
- 온-프레미스 머신의 경우 백업하려는 각 머신에 MARS 에이전트를 설치할 필요가 없습니다. 각 머신은 DPM/MABS 보호 에이전트를 실행하며, MARS 에이전트는 MABS/DPM에서만 실행됩니다.
- 백업 실행을 위한 더 유연하고 세분화된 일정 예약 옵션이 있습니다.
- 단일 콘솔에서 보호 그룹으로 모이는 여러 머신에 대한 백업을 관리할 수 있습니다. 이는 여러 머신에서 앱이 계층화되고 이러한 앱을 함께 백업하려는 경우에 특히 유용합니다.

백업 서버를 사용할 때 [백업의 작동 원리](backup-architecture.md#architecture-back-up-to-dpmmabs) 및 백업 서버의 [지원 요구 사항](backup-support-matrix-mabs-dpm.md)을 자세히 알아보세요.

## <a name="what-can-i-back-up"></a>어떤 것을 백업할 수 있나요?

**머신** | **백업 방법** | **백업**
--- | --- | ---
**온-프레미스 Windows VM** | MARS 에이전트 실행 | 파일, 폴더, 시스템 상태를 백업합니다.<br/><br/> Linux 머신은 지원되지 않습니다.
**온-프레미스 컴퓨터** | DPM/MABS에 백업 | 파일/폴더/공유/볼륨 및 앱별 데이터를 포함하여 [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) 또는 [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs)로 보호되는 모든 것을 백업합니다.
**Azure VM** | Azure VM 에이전트 백업 확장 실행 | 전체 VM 백업
**Azure VM** | MARS 에이전트 실행 | 파일, 폴더, 시스템 상태를 백업합니다.<br/><br/> Linux 머신은 지원되지 않습니다.
**Azure VM** | Azure에서 실행되는 MABS/DPM에 백업 | 파일/폴더/공유/볼륨 및 앱별 데이터를 포함하여 [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) 또는 [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807)으로 보호되는 모든 것을 백업합니다.

## <a name="what-backup-agents-do-i-need"></a>필요한 백업 에이전트는 무엇인가요?

**시나리오** | **에이전트**
--- | ---
**Azure VM 백업** | 에이전트가 필요 없습니다. 백업용 Azure VM 확장은 Azure VM 백업을 처음으로 실행할 때 Azure VM에 설치됩니다.<br/><br/> Windows 및 Linux 지원을 지원합니다.
**온-프레미스 Windows 머신 백업** | MARS 에이전트를 머신에 직접 다운로드하여 설치하고 실행합니다.
**MARS 에이전트를 사용하여 Azure VM 백업** | MARS 에이전트를 머신에 직접 다운로드하여 설치하고 실행합니다. MARS 에이전트는 백업 확장과 함께 실행할 수 있습니다.
**온-프레미스 머신 및 Azure VM을 DPM/MABS에 백업** | DPM 또는 MABS 보호 에이전트는 보호하려는 머신에서 실행됩니다. MARS 에이전트는 Azure에 백업할 DPM 서버/MABS에서 실행됩니다.

## <a name="which-backup-agent-should-i-use"></a>사용해야 하는 백업 에이전트는 무엇인가요?

**Backup** | **해결 방법** | **제한 사항**
--- | --- | ---
**전체 Azure VM을 백업하려는 경우** | VM에 백업을 사용하도록 설정합니다. 백업 확장은 Windows 또는 Linux Azure VM에서 자동으로 구성됩니다. | 전체 VM이 백업됩니다. <br/><br/> Windows VM의 경우 앱 일치 백업이 수행됩니다. Linux의 경우 파일 일치 백업이 수행됩니다. Linux VM에 대한 앱 인식이 필요한 경우 사용자 지정 스크립트를 사용하여 이를 구성해야 합니다.
**Azure VM의 특정 파일/폴더를 백업하려는 경우** | VM에 MARS 에이전트를 배포합니다.
**온-프레미스 Windows 머신을 직접 백업하려는 경우** | 머신에 MARS 에이전트를 설치합니다. | 파일, 폴더 및 시스템 상태를 Azure에 백업할 수 있습니다. 앱 인식 백업이 아닙니다.
**온-프레미스 Linux 머신을 직접 백업하려는 경우** | Azure에 백업하려면 DPM 또는 MABS를 배포해야 합니다. | Linux 호스트의 백업은 지원되지 않지만, Hyper-V 또는 VMWare에서 호스팅되는 Linux 게스트 머신만 백업할 수 있습니다.
**온-프레미스에서 실행되는 앱을 백업하려는 경우** | 앱 인식 백업의 경우 머신을 DPM 또는 MABS로 보호해야 합니다.
**Azure VM의 백업 및 복구를 세밀하고 유연하게 설정하려는 경우** | Azure에서 실행되는 MABS/DPM으로 Azure VM을 보호하면 백업 일정 예약을 보다 유연하게 처리하고 파일, 폴더, 볼륨, 앱 및 시스템 상태를 매우 유연하게 보호하고 복원할 수 있습니다.

## <a name="how-does-azure-backup-work-with-encryption"></a>Azure Backup은 암호화를 어떻게 처리하나요?

**암호화** | **온-프레미스 백업** | **Azure VM 백업** | **Azure VM에 SQL 백업**
--- | --- | --- | ---
휴지 상태의 암호화<br/> (유지/저장되는 데이터 암호화) | 고객이 지정한 암호는 데이터 암호화에 사용됩니다. | Azure [SSE(스토리지 서비스 암호화)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)는 자격 증명 모음에 저장된 데이터를 암호화하는 데 사용됩니다.<br/><br/> Backup은 데이터를 저장하기 전에 자동으로 암호화합니다. Azure Storage는 데이터를 검색하기 전에 데이터의 암호를 해독합니다. 사용자가 관리하는 키를 SSE에 사용하는 기능은 현재 지원되지 않습니다.<br/><br/> [ADE(Azure 디스크 암호화)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)를 사용하여 OS 및 데이터 디스크를 암호화하는 VM을 백업할 수 있습니다. Azure Backup은 BEK만 사용하여 암호화된 VM과 BEK 및 [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)를 사용하여 암호화된 VM을 지원합니다. [제한 사항](backup-azure-vms-encryption.md#encryption-support)을 검토하세요. | Azure Backup은 SQL Server 데이터베이스 또는 [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)가 설정된 서버의 백업을 지원합니다. Azure Backup은 Azure 관리형 키 또는 고객 관리형 키(BYOK)를 사용하는 TDE를 지원합니다.<br/><br/> Azure Backup은 백업 프로세스 중에 SQL 암호화를 수행하지 않습니다.
전송 중 암호화<br/> (한 위치에서 다른 위치로 이동하는 데이터의 암호화) | 데이터는 AES256을 사용하여 암호화된 후 HTTPS를 통해 Azure의 자격 증명 모음으로 전송됩니다. | Azure 내에서 Azure 스토리지와 자격 증명 모음 간에 이동하는 데이터는 HTTPS를 통해 보호됩니다. 이 데이터는 Azure 백본 네트워크에 남아 있습니다.<br/><br/> 파일 복구를 위해, iSCSI는 자격 증명 모음과 Azure VM 간에 전송되는 데이터를 보호합니다. 보안 터널링은 iSCSI 채널을 보호합니다. | Azure 내에서 Azure 스토리지와 자격 증명 모음 간에 이동하는 데이터는 HTTPS를 통해 보호됩니다.<br/><br/> 파일 복구는 SQL과 관련이 없습니다.

## <a name="next-steps"></a>다음 단계

- 다양한 백업 시나리오에 대한 아키텍처와 구성 요소를 [검토](backup-architecture.md)합니다.
- 백업에 지원되는 기능과 설정을 [확인](backup-support-matrix.md)합니다.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
