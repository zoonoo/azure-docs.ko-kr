---
title: Azure Backup이란?
description: Azure Backup 서비스에 대해 간략히 설명하고, BCDR(비즈니스 연속성 및 재해 복구) 전략의 일환으로 이를 배포하는 방법을 제공합니다.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 298c9fabca9d1994e0b952fdf8b48b70370c3ec2
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490673"
---
# <a name="what-is-azure-backup"></a>Azure Backup이란?

Azure Backup 서비스는 Microsoft Azure 클라우드에 데이터를 백업합니다. 온-프레미스 머신 및 워크로드와 Azure VM(가상 머신)을 백업할 수 있습니다.


## <a name="why-use-azure-backup"></a>Azure Backup을 사용하는 이유

Azure Backup에서 제공하는 주요 이점은 다음과 같습니다.

- **온-프레미스 백업 오프로드**: Azure Backup은 온-프레미스 리소스를 클라우드에 백업할 수 있는 간단한 솔루션을 제공합니다. 복잡한 온-프레미스 백업 솔루션을 배포할 필요 없이 장단기 백업을 수행합니다. 테이프 또는 오프사이트 백업을 수행할 필요가 없습니다.
- **Azure IaaS VM 백업**: Azure Backup은 실수로 데이터가 삭제되지 않도록 방지하기 위해 독립적이고 격리된 백업을 제공합니다. 백업은 복구 지점이 기본적으로 관리되는 Recovery Services 자격 증명 모음에 저장됩니다. 구성 및 확장성이 간단하고, 백업이 최적화되어 있으며, 필요에 따라 쉽게 복원할 수 있습니다.
- **손쉬운 크기 조정**: Azure Backup은 유지 관리 또는 모니터링 오버헤드 없이 기본 성능과 Azure 클라우드의 무제한 크기 조정을 사용하여 고가용성을 제공합니다. 이벤트 정보를 제공하도록 경고를 설정할 수 있지만 클라우드에 있는 데이터의 고가용성에 대해 걱정할 필요가 없습니다.
- **무제한 데이터 전송**: Azure Backup은 전송하는 인바운드 또는 아웃바운드 데이터의 양을 제한하지 않습니다. 또한 전송되는 데이터에 대해 요금을 청구하지 않습니다. 그러나 Azure Import/Export 서비스를 통해 대량의 데이터를 가져오는 경우 인바운드 데이터와 관련 된 비용이 발생합니다. 이 비용에 대한 자세한 내용은 [Azure Backup의 오프라인 백업 워크플로](backup-azure-backup-import-export.md)를 참조하세요. 아웃바운드 데이터는 복원 작업 중에 Recovery Services 자격 증명 모음에서 전송된 데이터입니다.
- **안전한 데이터 유지**: 데이터 암호화를 통해 공용 클라우드에서 데이터를 안전하게 전송하고 저장할 수 있습니다. 암호화 암호는 로컬에서 저장되며, Azure에서 전송되거나 저장되지는 않습니다. 데이터를 복원해야 하는 경우 암호화 암호 또는 키만 이으면 됩니다.
- **애플리케이션 일치 백업 가져오기**: 애플리케이션 일치 백업은 백업 복사본을 복원하는 데 필요한 모든 데이터가 복구 지점에 있음을 의미합니다. Azure Backup에서 애플리케이션 일치 백업을 제공하므로 데이터를 복원하기 위한 추가 수정 프로그램이 필요하지 않습니다. 애플리케이션 일치 데이터를 복원하면 실행 상태로 빠르게 돌아갈 수 있으므로 복원 시간을 줄여줍니다.
- **단기 및 장기 보존**: **장기 보존** - 단기 및 장기 데이터 보존을 위해 Recovery Services 자격 증명 모음을 사용할 수 있습니다. Azure는 Recovery Services 자격 증명 모음에 데이터를 유지할 수 있는 기간을 제한하지 않습니다. 원하는 만큼 자격 증명 모음에 데이터를 유지할 수 있습니다. Azure Backup에는 보호된 인스턴스당 9999개 복구 지점의 제한이 있습니다. 이 제한이 백업 필요성에 어떻게 영향을 줄 수 있는지에 대한 설명은 이 문서의 [Backup 및 보존](backup-introduction-to-azure-backup.md#backup-and-retention) 섹션을 참조하세요.
- **자동 저장소 관리** - 일부는 온-프레미스, 일부는 클라우드인 하이브리드 환경에는 종종 다른 유형의 저장소가 필요합니다. Azure Backup을 사용하면 온-프레미스 저장소 디바이스를 사용하기 위한 비용이 들지 않습니다. Azure Backup은 백업 저장소를 자동으로 할당하고 관리하며 사용한 만큼 지불(pay-as-you-use) 모델을 사용합니다. ‘사용한 만큼 지불’은 사용하는 저장소에 대한 요금만 지불한다는 의미입니다. 자세한 내용은 [Azure 가격 책정](https://azure.microsoft.com/pricing/details/backup)을 참조하세요.
- **여러 저장소 옵션** - 높은 가용성의 한 양상으로 저장소 복제가 있습니다. Azure Backup에는 두 가지 유형의 복제, 즉 [로컬 중복 저장소](../storage/common/storage-redundancy-lrs.md)와 [지역 중복 저장소](../storage/common/storage-redundancy-grs.md)가 있습니다. 필요에 따라 다음과 같은 백업 저장소 옵션을 선택합니다.
    - LRS(로컬 중복 저장소)는 데이터 센터의 저장소 배율 단위로 데이터를 세 번 복제합니다(세 개의 데이터 복사본을 만듦). 모든 데이터 복사본은 동일한 지역 내에 있습니다. LRS는 로컬 하드웨어 오류로부터 데이터를 보호하기 위한 저비용 옵션입니다.
    - GRS(지역 중복 저장소)는 기본값이며 권장되는 복제 옵션입니다. GRS는 데이터 원본의 기본 위치에서 수백 마일 떨어진 보조 지역으로 데이터를 복제합니다. GRS는 LRS보다 많은 비용이 소요되지만 GRS는 지역 가동 중단이 발생해도 높은 수준의 데이터 내구성을 제공합니다.


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Azure Backup과 Azure Site Recovery의 차이점은 무엇인가요?

Azure Backup 및 Azure Site Recovery 서비스는 모두 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. BCDR은 크게 다음 두 가지 목표로 구성됩니다.

- 중단이 발생하는 경우 비즈니스 데이터를 안전하게 유지하고 복구합니다.
- 계획되거나 계획되지 않은 가동 중지 동안 앱과 워크로드를 계속 가동하고 실행합니다.

두 서비스는 모두 보완적이지만 서로 다른 기능을 제공합니다.

- **Azure Site Recovery**: Site Recovery는 온-프레미스 머신 및 Azure VM에 대한 재해 복구 솔루션을 제공합니다. 머신을 주 위치에서 보조 위치로 복제합니다. 재해가 발생하면 머신을 보조 위치로 장애 조치하고 여기에서 액세스합니다. 모든 것이 정상적으로 다시 가동되고 실행되면 머신을 장애 복구하여 주 사이트에 복구합니다.
- **Azure Backup**: Azure Backup 서비스는 온-프레미스 머신 및 Azure VM에서 데이터를 백업합니다. 파일, 폴더, 머신 시스템 상태 및 앱 인식 데이터 백업을 포함한 세분화된 수준에서 데이터를 백업 및 복구할 수 있습니다. Azure Backup은 Site Recovery보다 더 세분화된 수준에서 데이터를 처리합니다. 예를 들어 랩톱의 프레젠테이션이 손상된 경우 Azure Backup을 사용하여 해당 프레젠테이션을 복원할 수 있습니다. VM 구성 및 데이터를 안전하게 유지하고 액세스할 수 있도록 하려면 Site Recovery를 사용할 수 있습니다.  

다음 표의 항목을 사용하여 BCDR 요구 사항을 파악합니다. 

**목표** | **세부 정보** | **비교**
--- | --- | --- | --- |
**데이터 백업/보존** | 규정 준수 관점에서 필요한 경우 백업 데이터를 며칠, 몇 개월 또는 심지어 몇 년 동안 유지하고 저장할 수 있습니다. | Azure Backup과 같은 백업 솔루션을 사용하면 백업하려는 데이터를 세밀하게 선택하고 백업 및 보존 정책을 미세하게 튜닝할 수 있습니다.<br/><br/> Site Recovery는 이와 같은 미세 튜닝을 허용하지 않습니다.
**RPO(복구 지점 목표)** | 복구 작업을 수행해야 할 때 용인되는 데이터 손실의 양입니다. | 백업에는 더 가변적인 RPO가 있습니다.<br/><br/> 일반적으로 VM 백업은 1일의 RPO를 가지며 데이터베이스 백업은 15분 정도의 RPO를 갖습니다.<br/><br/> 복제가 연속적이거나 빈번하므로 Site Recovery에서는 낮은 RPO를 제공합니다. 이로 인해 원본과 복제본 복사본 간의 델타가 작습니다.
**RTO(복구 시간 목표)** |복구 또는 복원을 완료하는 데 걸리는 시간의 양입니다. | 더 큰 RPO로 인해 백업 솔루션이 처리해야 하는 데이터의 양이 일반적으로 훨씬 더 많고 결과적으로 RTO가 더 길어집니다. 예를 들어 테이프에서 데이터를 복원하면 오프사이트 위치에서 테이프를 전송하는 데 걸리는 시간에 따라 몇 일이 걸릴 수 있습니다. | 연속적이거나 빈번한 복제는 일반적으로 대상이 원본과 더 많이 동기화됨을 의미하므로 Site Recovery와 같은 재해 복구 솔루션은 RPO가 낮습니다. |

## <a name="what-backup-scenarios-are-supported"></a>지원되는 백업 시나리오는 무엇인가요?

Azure Backup은 온-프레미스 머신과 Azure VM을 모두 백업할 수 있습니다.

**머신** | **백업 시나리오**
--- | ---
**온-프레미스 머신(실제/가상)** |  개별 온-프레미스 머신을 백업할 수 있습니다.<br/><br/>System Center DPM(Data Protection Manager)으로 보호되는 온-프레미스 머신을 백업할 수 있습니다.<br/><br/> MABS(Microsoft Azure Backup 서버)로 보호되는 온-프레미스 머신을 백업할 수 있습니다.
**Azure VM** | 개별 Azure VM을 백업할 수 있습니다.<br/><br/> DPM 또는 MABS로 보호되는 Azure VM을 백업할 수 있습니다.

### <a name="back-up-servers"></a>백업 서버

온-프레미스 서버와 해당 워크로드 또는 Azure VM과 해당 워크로드를 먼저 백업 서버에 백업한 다음, Recovery Services 자격 증명 모음에 백업할 수 있습니다. 

**백업 서버** | **세부 정보**
--- | ---
**System Center DPM(Data Protection Manager)** | Azure Backup을 사용하여 DPM으로 보호되는 데이터를 백업할 수 있습니다.<br/><br/> - DPM이 온-프레미스(실제 또는 가상) 또는 Azure에서 실행될 수 있습니다.<br/><br/> - 데이터를 DPM 서버에 백업하여 온-프레미스 머신과 Azure VM에서 실행되는 다양한 형식의 데이터를 보호할 수 있습니다.<br/><br/> 그러면 Azure Backup 서비스를 사용하여 DPM 서버를 Recovery Services 자격 증명 모음에 백업할 수 있습니다.<br/><br/> DPM 서버와 이 서버에서 보호하는 머신이 동일한 네트워크에 있어야 합니다. 온-프레미스 머신은 온-프레미스 DPM 서버에서만 보호할 수 있습니다. 마찬가지로, Azure VM은 Azure에서 실행되는 DPM에서만 보호할 수 있습니다.
**MABS(Microsoft Azure Backup 서버)** | Azure Backup을 사용하여 MABS로 보호되는 데이터를 백업할 수 있습니다.<br/><br/> - MABS가 온-프레미스(실제 또는 가상) 또는 Azure에서 실행될 수 있습니다.<br/><br/> - 데이터를 MABS에 백업하여 온-프레미스 머신과 Azure VM에서 실행되는 다양한 형식의 데이터를 보호할 수 있습니다.<br/><br/> - 그러면 Azure Backup 서비스를 사용하여 MABS를 Recovery Services 자격 증명 모음에 백업할 수 있습니다.<br/><br/> - MABS는 MABS를 사용하여 테이프에 백업할 수 없다는 점을 제외하면 DPM과 비슷한 기능을 제공합니다. MABS에는 System Center 라이선스가 필요하지 않습니다.<br/><br/> DPM과 마찬가지로, 온-프레미스 머신은 온-프레미스 MABS에서만 보호할 수 있습니다. Azure VM은 Azure의 MABS에서만 보호할 수 있습니다.

먼저 DPM/MABS에 백업한 다음, 자격 증명 모음에 백업하는 이점은 다음과 같습니다.

- DPM/MAB에 백업하면 파일/폴더/볼륨 백업 및 머신 상태 백업(완전 백업, 시스템 상태) 외에도 SQL Server, Exchange 및 SharePoint와 같은 일반적인 앱에 최적화된 앱 인식 백업을 제공합니다.
- 백업하려는 각 머신에 Azure Backup 에이전트를 설치할 필요가 없습니다. 각 머신에서 DPM/MABS 보호 에이전트를 실행하고, Azure Backup Microsoft Azure Recovery Services 에이전트는 DPM 서버/MABS에서만 실행됩니다.
- 백업 실행을 위한 더 유연하고 세분화된 일정 예약 옵션이 있습니다.
- 단일 콘솔에서 보호 그룹으로 모이는 여러 머신에 대한 백업을 관리할 수 있습니다. 이는 여러 머신에서 앱이 계층화되고 이러한 앱을 함께 백업하려는 경우에 특히 유용합니다.

## <a name="what-can-be-backed-up"></a>백업할 수 있는 대상은 무엇인가요?

**머신** | **백업 서버** | **백업**
--- | --- | ---
온-프레미스 Windows VM | DPM 또는 MABS에 백업되지 않음 | 파일, 폴더, 시스템 상태를 백업합니다.
Azure VM(Windows 및 Linux) | DPM 또는 MABS에 백업되지 않음 | 파일, 폴더, 시스템 상태를 백업합니다.<br/><br/> Windows 머신의 경우 앱 인식 백업이고, Linux 머신의 경우 파일 인식 백업입니다.
온-프레미스 VM/Azure VM | DPM에서 보호 | 파일/폴더/공유/볼륨 및 앱별 데이터를 포함하여 DPM에서 보호하는 모든 데이터를 백업합니다. DPM으로 백업할 수 대상을 [알아봅니다](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).
온-프레미스 VM/Azure VM | MABS에서 보호 | 파일/폴더/공유/볼륨 및 앱별 데이터를 포함하여 MABS에서 보호하는 모든 데이터를 백업합니다. MABS에서 백업할 수 있는 대상을 [알아봅니다](backup-mabs-protection-matrix.md).

## <a name="what-backup-agents-do-i-need"></a>필요한 백업 에이전트는 무엇인가요?
**시나리오** | **에이전트** | **세부 정보**
--- | --- | ---
온-프레미스 머신(백업 서버 없음) | MARS(Microsoft Azure Recovery Services) 에이전트는 Windows 머신에서 실행됩니다. | Azure Backup 배포 중에 MARS 에이전트를 다운로드하여 설치합니다.<br/><br/> Windows 머신만 지원합니다.
Azure VM(백업 서버 없음) | 명시적 에이전트가 필요하지 않습니다. 백업용 Azure VM 확장이 Azure VM에서 실행됩니다. | 확장은 Azure VM 백업을 처음 실행할 때 설치됩니다.<br/><br/> Windows 및 Linux 지원을 지원합니다.
DPM에서 보호하는 온-프레미스 머신/Azure VM | MARS 에이전트가 DPM 서버에서 실행됩니다. | 개별 머신에 MARS 에이전트가 필요하지 않습니다.<br/><br/> DPM을 배포할 때 보호하는 각 머신에 DPM 보호 에이전트가 설치됩니다. 
MABS에서 보호하는 온-프레미스 머신 및 Azure VM 백업 | MARS 에이전트가 MABS에서 실행됩니다. | 개별 머신에 MARS 에이전트가 필요하지 않습니다.<br/><br/> MABS를 배포할 때 보호하는 각 머신에 MABS 보호 에이전트가 설치됩니다. 


## <a name="which-backup-agent-should-i-use"></a>사용해야 하는 백업 에이전트는 무엇인가요?

**Backup** | **해결 방법** | **제한 사항**
--- | --- | ---
온-프레미스 Windows 머신을 백업하려고 합니다. 머신은 DPM 또는 MABS에서 보호하지 않습니다. | 머신에 MARS 에이전트를 설치합니다. | 파일, 폴더 및 시스템 상태를 Azure에 백업할 수 있습니다. 앱 인식 백업이 아닙니다.
온-프레미스 Linux 머신을 백업하려고 합니다. 머신은 DPM 또는 MABS에서 보호하지 않습니다. | Azure에 백업하려면 DPM 또는 MABS를 배포해야 합니다.
온-프레미스 Windows 머신에서 실행되는 앱을 백업하려고 합니다. | 앱 인식 백업의 경우 Windows 머신은 DPM 또는 MABS에서 보호해야 합니다.
Azure VM을 백업하려고 합니다. | Azure Backup을 사용하여 백업을 실행합니다. 백업 확장은 Windows 또는 Linux Azure VM에서 자동으로 구성됩니다. | VM 디스크가 백업됩니다.<br/><br/> Windows VM의 경우 앱 일치 백업이 수행됩니다. Linux의 경우 파일 일치 백업이 수행됩니다. 앱 인식 백업이 필요한 경우 사용자 지정 스크립트를 사용하여 이를 구성해야 합니다.
백업 및 복구 설정의 세분화된 유연성을 사용하여 Azure VM을 백업하려고 합니다. | Azure에서 실행되는 DPM 또는 MABS를 통해 Azure VM을 보호하면 백업 일정 예약을 더 유연하게 처리하고 파일, 폴더, 볼륨, 앱 및 시스템 상태를 완벽하게 보호 및 복원할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 다양한 백업 시나리오에 대한 아키텍처와 구성 요소를 [검토](backup-architecture.md)합니다.
- 백업에 지원되는 기능과 설정을 [확인](backup-support-matrix.md)합니다.

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

