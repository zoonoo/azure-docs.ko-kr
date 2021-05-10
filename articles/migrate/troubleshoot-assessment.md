---
title: Azure Migrate의 평가 및 종속성 시각화 문제 해결
description: Azure Migrate의 평가 및 종속성 시각화를 지원합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 78e54543c94cd6e8434023b61516242c2491f353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863601"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>평가/종속성 시각화 문제 해결

이 문서는 [Azure Migrate: 검색 및 평가](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)를 사용하여 평가 및 종속성 시각화 문제를 해결하는 데 도움이 됩니다.


## <a name="assessment-readiness-issues"></a>평가 준비 문제

평가 준비 문제를 다음과 같이 수정합니다.

**문제점** | **해결**
--- | ---
지원되지 않는 부팅 유형 | Azure는 공유 EFI 부팅 유형의 VM을 지원하지 않습니다. 마이그레이션을 실행하기 전에 부팅 유형을 BIOS로 변환하는 것이 좋습니다. <br/><br/>Azure Migrate 서버 마이그레이션을 사용하여 이러한 VM의 마이그레이션을 처리할 수 있습니다. 마이그레이션하는 동안 VM의 부팅 유형을 BIOS로 변환합니다.
조건부로 지원되는 Windows 운영 체제 | 이 운영 체제는 지원 날짜가 종료되었으며, [Azure에서 지원](/troubleshoot/azure/virtual-machines/server-software-support)을 받기 위해 CSA(사용자 지정 지원 계약)가 필요합니다. Azure로 마이그레이션하기 전에 업그레이드하는 것이 좋습니다. Azure로 마이그레이션하기 위해 [Windows Server 2003을 실행하는 서버 준비](prepare-windows-server-2003-migration.md)에 대한 정보를 검토합니다.
지원되지 않는 Windows 운영 체제 | Azure는 [선택한 Windows OS 버전](/troubleshoot/azure/virtual-machines/server-software-support)만 지원합니다. Azure로 마이그레이션하기 전에 서버를 업그레이드하는 것이 좋습니다.
조건부로 보증되는 Linux OS | Azure에서는 [선택한 LINUX OS 버전](../virtual-machines/linux/endorsed-distros.md)만 보증합니다. Azure로 마이그레이션하기 전에 서버를 업그레이드하는 것이 좋습니다. 자세한 내용은 [여기](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment)를 참조하세요.
보증되지 않는 Linux OS | 이 서버는 Azure에서 시작될 수 있지만, Azure는 운영 체제를 지원하지 않습니다. Azure로 마이그레이션하기 전에 [보증된 Linux 버전](../virtual-machines/linux/endorsed-distros.md)으로 업그레이드하는 것이 좋습니다.
알 수 없는 운영 체제 | vCenter Server에서는 VM의 운영 체제가 "기타"로 지정되었습니다. 이 동작 때문에 Azure Migrate에서 VM의 Azure 준비 상태를 확인하지 못합니다. 서버를 마이그레이션하기 전에 Azure에서 운영 체제가 [지원되는지](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) 확인합니다.
지원되지 않는 비트 버전 | 32비트 운영 체제를 사용하는 VM은 Azure에서 부팅될 수 있지만, Azure로 마이그레이션하기 전에 64비트로 업그레이드하는 것이 좋습니다.
Microsoft Visual Studio 구독이 필요합니다. | 서버에서는 Visual Studio 구독을 통해서만 지원되는 Windows 클라이언트 운영 체제를 실행하고 있습니다.
필요한 스토리지 성능을 제공하는 VM을 찾을 수 없음 | 서버에 필요한 스토리지 성능(IOPS(초당 입/출력 작업 수) 및 처리량)이 Azure VM 지원을 초과합니다. 마이그레이션하기 전에 서버에 대한 스토리지 요구 사항을 줄입니다.
필요한 네트워크 성능을 제공하는 VM을 찾을 수 없음 | 서버에 필요한 네트워크 성능(입력/출력)이 Azure VM 지원을 초과합니다. 서버의 네트워킹 요구 사항을 낮춰봅니다.
지정된 위치에서 VM을 찾을 수 없음 | 다른 대상 위치를 사용하여 마이그레이션을 실행해 봅니다.
부적합한 디스크가 하나 이상 있음 | VM에 연결된 하나 이상의 디스크가 Azure 요구 사항을 충족하지 않습니다.<br/><br/> Azure Migrate: 검색 및 평가는 현재 울트라 SSD 디스크를 지원하지 않으며 프리미엄 Managed Disks에 대한 디스크 제한(32TB)에 따라 디스크를 평가합니다.<br/><br/> VM에 연결된 각 디스크에 대해 디스크 크기가 64TB 미만인지 확인합니다(울트라 SSD 디스크에서 지원됨).<br/><br/> 그렇지 않은 경우 Azure로 마이그레이션하기 전에 디스크 크기를 줄이거나, Azure에서 여러 디스크를 사용하고 [함께 스트라이프](../virtual-machines/premium-storage-performance.md#disk-striping)하여 스토리지 제한을 더 늘려야 합니다. 각 디스크에 필요한 성능(IOPS 및 처리량)이 Azure [관리 가상 머신 디스크](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)에서 지원되는지 확인합니다.
부적합한 네트워크 어댑터가 하나 이상 있습니다. | 마이그레이션을 시작하기 전에 서버에서 사용하지 않는 네트워크 어댑터를 제거합니다.
디스크 수 한도 초과 | 마이그레이션을 시작하기 전에 서버에서 사용하지 않는 디스크를 제거합니다.
디스크 크기 한도 초과 | Azure Migrate: 검색 및 평가는 현재 울트라 SSD 디스크를 지원하지 않으며 프리미엄 디스크 제한(32TB)에 따라 디스크를 평가합니다.<br/><br/> 그러나 Azure는 최대 64TB 크기의 디스크를 지원합니다(울트라 SSD 디스크에서 지원됨). 마이그레이션을 수행하기 전에 디스크를 64TB 미만으로 축소하거나 Azure에서 여러 디스크를 사용하고 [함께 스트라이프](../virtual-machines/premium-storage-performance.md#disk-striping)하여 스토리지 제한을 더 늘립니다.
지정된 위치의 디스크를 사용할 수 없음 | 마이그레이션하기 전에 디스크가 대상 위치에 있는지 확인합니다.
지정된 이중화에 디스크를 사용할 수 없음 | 디스크가 평가 설정에 정의된 중복 스토리지 형식(기본적으로 LRS)을 사용해야 합니다.
내부 오류로 인해 디스크 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
필요한 코어 및 메모리가 포함된 VM을 찾을 수 없음 | Azure에서 적합 한 VM 유형을 찾을 수 없습니다. 마이그레이션하기 전에 온-프레미스 서버의 메모리 및 코어 수를 줄여봅니다.
내부 오류로 인해 VM 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 디스크 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
내부 오류로 인해 하나 이상의 네트워크 어댑터 적합성을 확인할 수 없음 | 그룹에 대한 새 평가를 만들어 봅니다.
제품 통화 예약 인스턴스에 대한 VM 크기를 찾을 수 없음 | 선택된 RI, 제품 및 통화 조합에 대한 VM 크기를 찾을 수 없기 때문에 서버가 적합하지 않은 것으로 표시되었습니다. 평가 속성을 편집하여 유효한 조합을 선택하고 평가를 다시 계산합니다. 
조건부 준비 완료된 인터넷 프로토콜 | AVS(Azure VMware Solution) 평가에만 적용됩니다. AVS는 IPv6 인터넷 주소 요소를 지원하지 않습니다. 서버가 IPv6에서 감지되면 AVS 팀에서 수정 지침을 문의하세요.

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>제안된 마이그레이션 도구가 가져오기 기반 AVS 평가에서 알 수 없음으로 표시됨

CSV 파일을 통해 가져온 서버의 경우 기본 마이그레이션 도구 및 AVS 평가를 알 수 없습니다. VMware 환경의 서버에서 VMware HCX(하이브리드 클라우드 확장) 솔루션을 사용하는 것이 좋습니다. [자세한 정보](../azure-vmware/tutorial-deploy-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux VM은 Azure VM 평가에서 "조건부로 준비"됩니다.

VMware 및 Hyper-V VM의 경우 Azure VM 평가는 알려진 간격으로 인해 Linux VM을 "조건부로 준비" 상태로 표시합니다. 

- 이 간격 때문에 온-프레미스 VM에 설치된 Linux OS의 부 버전을 검색하지 못하게 됩니다.
- 예를 들어, RHEL 6.10의 경우 현재 Azure VM 평가는 RHEL 6만 OS 버전으로 검색합니다. Hyper-V 호스트의 vCenter Server가 Linux VM 운영 체제의 커널 버전을 제공하지 않기 때문입니다.
-  Azure가 특정 Linux 버전만 보증하므로, 현재 Linux VM은 Azure VM 평가에서 조건부 준비 완료로 표시됩니다.
- [Azure Linux 지원](../virtual-machines/linux/endorsed-distros.md)을 검토하여 온-프레미스 VM에서 실행되는 Linux OS가 Azure에서 보증되는지 여부를 확인할 수 있습니다.
-  보증되는 배포판을 확인한 후에는 이 경고를 무시해도 됩니다.

이 간격은 VMware VM에서 [애플리케이션 검색](./how-to-discover-applications.md)을 사용하도록 설정하여 해결할 수 있습니다. Azure VM 평가는 제공된 게스트 자격 증명을 사용하여 VM에서 검색된 운영 체제를 사용합니다. 이 운영 체제 데이터는 Windows 및 Linux VM 둘 다에 대해 적절한 OS 정보를 식별합니다.

## <a name="operating-system-version-not-available"></a>운영 체제 버전을 사용할 수 없음

물리적 서버의 경우 운영 체제 부 버전 정보를 사용할 수 있습니다. 사용할 수 없는 경우 Microsoft 지원 서비스에 문의하세요. VMware 환경의 서버에 대해 Azure Migrate는 vCenter Server의 VM에 대해 지정된 운영 체제 정보를 사용합니다. 그러나 vCenter Server는 운영 체제의 부 버전을 제공하지 않습니다. 부 버전을 검색하려면 [애플리케이션 검색](./how-to-discover-applications.md)을 설정해야 합니다. Hyper-V VM의 경우 운영 체제 부 버전 검색은 지원되지 않습니다. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 평가에서 Azure SKU가 온-프레미스보다 더 큼

Azure VM 평가는 평가 유형에 따라 현재 온-프레미스 할당보다 더 많은 코어 및 메모리를 포함하는 Azure VM SKU를 권장할 수 있습니다.

- VM SKU 권장 사항은 평가 속성에 따라 달라집니다.
- 이러한 권장 사항은 Azure VM 평가에서 수행하는 평가 유형인 *성능 기반* 또는 *온-프레미스로* 의 영향을 받습니다.
- 성능 기반 평가의 경우 Azure VM 평가는 온-프레미스 VM의 데이터 사용률(CPU, 메모리, 디스크 및 네트워크 사용률)을 고려하여 온-프레미스 VM에 적절한 대상 VM SKU를 결정합니다. 또한 효과적인 사용률을 결정할 때 쾌적 인자를 추가합니다.
- 온-프레미스 크기 조정의 경우 성능 데이터는 고려되지 않으며, 대상 SKU는 온-프레미스 할당을 기반으로 하는 것이 좋습니다.

권장 구성에 영향을 줄 수 있는 방법을 알아보기 위해 예를 살펴보겠습니다.

4코어 8GB 메모리를 사용하고, CPU 사용률과 메모리 사용률은 각각 50%이고, 쾌적 인자가 1.3으로 지정된 온-프레미스 VM이 있습니다.

-  평가가 **온-프레미스로** 인 경우 4코어와 8GB 메모리를 사용하는 Azure VM SKU를 사용하는 것이 좋습니다.
- 평가가 효과적인 CPU 및 메모리 사용률(4코어의 50% *1.3 = 2.6코어 및 8GB 메모리의 50% * 1.3 = 5.3GB 메모리)을 기준으로 하는 성능 기반인 경우 4코어(최근 지원되는 코어 수) 및 8GB 메모리(최근 지원되는 메모리 크기)의 가장 저렴한 VM SKU를 사용하는 것이 좋습니다.
- 평가 크기 조정에 대해 [자세히 알아보세요](concepts-assessment-calculation.md#types-of-assessments).

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure VM 평가에서 권장되는 Azure Disk SKU가 온-프레미스보다 큰 이유는 무엇인가요?

Azure VM 평가는 평가 유형에 따라 더 큰 디스크를 권장할 수 있습니다.
- 디스크 크기 조정은 크기 조정 기준 및 스토리지 유형이라는 두 가지 평가 속성에 따라 달라집니다.
- 크기 조정 기준이 **성능 기반** 이고 스토리지 유형이 **자동** 으로 설정되면 디스크의 IOPS 및 처리량 값을 고려하여 대상 디스크 유형(표준 HDD, 표준 SSD 또는 프리미엄)을 결정합니다. 디스크 유형의 디스크 SKU가 권장되며 이러한 권장 사항에 온-프레미스 디스크의 크기 요구 사항이 고려됩니다.
- 크기 조정 기준이 **성능 기반** 이고 스토리지 유형이 **프리미엄** 이면 온-프레미스 디스크의 IOPS, 처리량 및 크기 요구 사항에 따라 Azure의 프리미엄 디스크 SKU를 사용하는 것이 좋습니다. 크기 조정 기준이 **온-프레미스로** 이고 스토리지 유형이 **표준 HDD**, **표준 SSD** 또는 **프리미엄** 인 경우에도 동일한 논리를 사용하여 디스크 크기를 결정합니다.

예를 들어 온-프레미스 디스크의 메모리가 32GB이지만 디스크의 읽기 및 쓰기를 합산한 IOPS가 800인 경우 Azure VM 평가는 프리미엄 디스크를 추천(IOPS 요구 사항이 높기 때문에)한 다음, 필요한 IOPS 및 크기를 지원할 수 있는 디스크 SKU를 추천합니다. 이 예제에서 가장 가까운 일치 항목은 P15(256GB, 1100IOPS)입니다. 따라서 온-프레미스 디스크에 필요한 크기가 32GB였지만 Azure VM 평가는 온-프레미스 디스크의 높은 IOPS 요구 사항 때문에 더 큰 디스크를 권장합니다.

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>평가 보고서에서 일부 또는 모든 VM의 성능 데이터가 누락된 이유는 무엇인가요?

"성능 기반" 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 VM에 대한 성능 데이터를 수집할 수 없는 경우 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 VM의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터가 누락되었지만 Hyper-V VM을 평가하려는 경우 이러한 VM에서 동적 메모리를 사용하도록 설정했는지 확인합니다. 현재 Azure Migrate 어플라이언스가 이러한 VM의 메모리 사용률을 수집할 수 없기 때문에 알려진 문제가 있습니다.
- 모든 성능 카운터가 누락된 경우 평가에 대한 포트 액세스 요구 사항이 충족되는지 확인합니다. [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) 및 [물리적](./migrate-support-matrix-physical.md#port-access) 평가를 위한 포트 액세스 요구 사항에 대해 자세히 알아봅니다.
참고- 임의의 성능 카운터가 누락된 경우 Azure Migrate: 검색 및 평가는 할당된 코어/메모리 온-프레미스로 대체하고 해당하는 VM 크기를 권장합니다.

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Azure VM 및/또는 AVS 평가 보고서에서 일부/모든 VM의 성능 데이터가 누락된 이유는 무엇인가요?

"성능 기반" 평가의 경우 Azure Migrate 어플라이언스에서 온-프레미스 서버에 대한 성능 데이터를 수집할 수 없는 경우 평가 보고서 내보내기에 'PercentageOfCoresUtilizedMissing' 또는 'PercentageOfMemoryUtilizedMissing'이 표시됩니다. 다음을 확인하세요.

- 평가를 만드는 동안 서버의 전원이 켜져 있는지 확인합니다.
- 메모리 카운터가 누락되었지만 Hyper-V 환경의 서버를 평가하려고 합니다. 이 시나리오에서는 서버에서 동적 메모리를 사용하도록 설정하고 평가를 '다시 계산'하여 최신 변경 내용을 반영하세요. 서버에서 동적 메모리를 사용하도록 설정한 경우에만 어플라이언스에서 Hyper-V 환경의 서버에 대해서만 메모리 사용률 값을 수집할 수 있습니다.

- 모든 성능 카운터가 누락된 경우 포트 443(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

    > [!Note]
    > 임의의 성능 카운터가 누락된 경우 Azure Migrate: 검색 및 평가는 할당된 코어/메모리 온-프레미스로 대체하고 해당하는 VM 크기를 권장합니다.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>내 Azure SQL 평가에서 일부/모든 SQL 인스턴스/데이터베이스에 대한 성능 데이터가 누락되는 이유는 무엇인가요?

성능 데이터가 수집되는지 확인하려면 다음을 확인하세요.

- 평가를 만드는 동안 SQL Server의 전원이 켜져 있는지 확인합니다.
- Azure Migrate에서 SQL 에이전트의 연결 상태가 ‘연결됨’이면 마지막 하트비트를 확인합니다. 
- 검색된 SQL 인스턴스 블레이드에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 ‘연결됨’인지 확인합니다.
- 모든 성능 카운터가 누락된 경우 포트 443(HTTPS)에서 아웃바운드 연결이 허용되는지 확인합니다.

성능 카운터가 없는 경우 Azure SQL 평가는 해당 인스턴스/데이터베이스에 대해 가장 작은 Azure SQL 구성을 권장합니다.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>내 평가의 신뢰 등급이 낮은 이유는 무엇인가요?

신뢰 등급은 평가를 계산하는 데 필요한 [사용 가능한 데이터 요소](./concepts-assessment-calculation.md#ratings)의 백분율을 기준으로 "성능 기반" 평가에 대해 계산됩니다. 아래에는 평가의 신뢰 등급이 낮아질 수 있는 이유가 나와 있습니다.

- 평가를 작성하는 기간 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 일주일로 설정하여 평가를 만드는 경우 검색 시작 후 일주일 이상 기다려야 데이터 요소가 수집됩니다. 이 기간 동안 기다릴 수 없으면 성능 기간을 더 짧은 기간으로 변경하고 평가를 **다시 계산** 하세요.
 
- 평가는 일부 또는 모든 서버에 대한 성능 데이터를 평가 기간에 수집할 수 없습니다. 높은 신뢰 등급의 경우 다음을 확인하세요. 
    - 평가 기간 동안 서버 전원이 켜져 있는지 확인합니다.
    - 포트 443에 대한 아웃바운드 연결이 허용되는지 확인합니다.
    - Hyper-V 서버의 경우 동적 메모리가 사용하도록 설정되어 있는지 확인합니다. 
    - Azure Migrate에서 에이전트의 연결 상태가 ‘연결됨’이면 마지막 하트비트를 확인합니다.
    - Azure SQL 평가의 경우 검색된 SQL 인스턴스 블레이드에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 “연결됨”인지 확인합니다.

    신뢰 등급의 최신 변경 내용을 반영하려면 평가를 **다시 계산** 하세요.

- Azure VM 및 AVS 평가의 경우 검색을 시작한 후 약간의 서버가 생성되었습니다. 예를 들어 마지막 1달의 성능 기록에 대한 평가를 만들려고 하는데, 일부 서버가 불과 일주일 전에 환경에서 생성되었습니다. 이 경우 새 서버의 성능 데이터를 전체 기간에 사용할 수 없으며 신뢰 등급이 낮아집니다. [자세한 정보](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Azure SQL 평가의 경우 검색을 시작한 후 일부 SQL 인스턴스 또는 데이터베이스가 만들어졌습니다. 예를 들어 마지막 1달의 성능 기록에 대한 평가를 만들려고 하는데, 일부 SQL 인스턴스 또는 데이터베이스가 불과 일주일 전에 환경에서 생성되었습니다. 이 경우 새 서버의 성능 데이터를 전체 기간에 사용할 수 없으며 신뢰 등급이 낮아집니다. [자세한 정보](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Azure VM 평가에 운영 체제 라이선스가 포함되어 있나요?

Azure VM 평가는 현재 Windows Server에 대한 운영 체제 라이선스 비용도 고려합니다. Linux 서버에 대한 라이선스 비용은 현재 고려되지 않습니다.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>Azure VM 평가에서 성능 기반 크기 조정은 어떻게 작동하나요?

Azure AM 평가는 온-프레미스 서버의 성능 데이터를 지속적으로 수집하여 Azure에서 VM SKU 및 디스크 SKU를 추천하는 데 사용합니다. 성능 기반 데이터가 수집되는 [방법을 알아봅니다](concepts-assessment-calculation.md#calculate-sizing-performance-based).

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>평가가 잘못된 예약 인스턴스, VM 가동 시간 및 할인율(%) 조합을 사용하여 생성되었다는 경고를 표시하는 이유는 무엇인가요?
'예약 인스턴스'를 선택하면 '할인율(%)' 및 'VM 가동 시간' 속성은 적용되지 않습니다. 이러한 속성의 잘못된 조합을 사용하여 평가를 만든 후에는 편집 및 다시 계산 단추가 사용하지 않도록 설정됩니다. 새 평가를 만드세요. [자세히 알아보기](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>실제 서버의 일부 네트워크 어댑터에 대한 성능 데이터가 표시되지 않습니다.

물리적 서버에서 Hyper-V 가상화를 사용하도록 설정한 경우에 이 문제가 발생할 수 있습니다. 이러한 서버에서는 제품 간격 때문에 현재 Azure Migrate에서는 물리적 및 가상 네트워크 어댑터를 모두 검색합니다. 네트워크 처리량은 검색된 가상 네트워크 어댑터에만 캡처됩니다.

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>물리적 서버의 권장되는 Azure VM SKU가 너무 큰 경우

물리적 서버에서 Hyper-V 가상화를 사용하도록 설정한 경우에 이 문제가 발생할 수 있습니다. 이러한 서버에서는 현재 Azure Migrate에서는 물리적 및 가상 네트워크 어댑터를 모두 검색합니다. 따라서 검색되는 네트워크 어댑터 수가 실제보다 높습니다. Azure VM 평가는 필요한 수의 네트워크 어댑터를 지원할 수 있는 Azure VM을 선택하므로 잠재적으로 VM 크기가 커질 수 있습니다. 크기 조정에 네트워크 어댑터 수가 미치는 영향에 대해 [자세히 알아봅니다](./concepts-assessment-calculation.md#calculating-sizing) . 이것은 앞으로 해결될 제품 간격에 해당합니다.

## <a name="readiness-category-not-ready-for-my-physical-server"></a>물리적 서버에 대한 준비 상태 범주가 "준비 안 됨"임

Hyper-V 가상화를 사용하도록 설정한 물리적 서버의 경우 준비 상태 범주가 "준비 안 됨"으로 잘못 표시될 수 있습니다. 이러한 서버에서는 제품 간격 때문에 현재 Azure Migrate에서는 물리적 및 가상 어댑터를 모두 검색합니다. 따라서 검색되는 네트워크 어댑터 수가 실제보다 높습니다. 온-프레미스로 및 성능 기반 평가에서 Azure VM 평가는 필요한 수의 네트워크 어댑터를 지원할 수 있는 Azure VM을 선택합니다. 네트워크 어댑터 수가 Azure VM에서 지원되는 NIC의 최대 수인 32보다 큰 것으로 확인되면 서버는 "준비 안 됨" 상태로 표시됩니다.  크기 조정에 NIC 수가 미치는 영향에 대해 [자세히 알아봅니다](./concepts-assessment-calculation.md#calculating-sizing) .


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>물리적 서버에 대해 실제보다 높은 수의 NIC가 검색됨

물리적 서버에서 Hyper-V 가상화를 사용하도록 설정한 경우에 이 문제가 발생할 수 있습니다. 이러한 서버에서는 현재 Azure Migrate에서는 물리적 및 가상 어댑터를 모두 검색합니다. 따라서 검색되는 NIC의 수가 실제보다 더 높습니다.

## <a name="dependency-visualization-in-azure-government"></a>Azure Government의 종속성 시각화

에이전트 기반 종속성 분석은 Azure Government에서 지원되지 않습니다. 에이전트 없는 종속성 분석을 사용하세요.


## <a name="dependencies-dont-show-after-agent-install"></a>에이전트 설치 후 종속성이 표시되지 않음

온-프레미스 VM에서 종속성 시각화 에이전트를 설치하고 나면 Azure Migrate는 일반적으로 포털에서 종속성을 표시하는 데 15-30분이 소요됩니다. 30분 넘게 대기한 경우 MMA(Microsoft Monitoring Agent)가 Log Analytics 작업 영역에 연결할 수 있는지 확인합니다.

Windows VM의 경우
1. 제어판에서 MMA를 시작합니다.
2. **Microsoft Monitoring Agent 속성** > **Azure Log Analytics(OMS)** 에서 작업 영역의 **상태** 가 녹색인지 확인합니다.
3. 상태가 녹색이 아닌 경우 작업 영역을 제거했다가 MMA에 다시 추가하세요.

    ![MMA 상태](./media/troubleshoot-assessment/mma-properties.png)

Linux VM의 경우 MMA 및 종속 에이전트 설치 명령이 정상적으로 실행되었는지 확인합니다. [여기](../azure-monitor/vm/service-map.md#post-installation-issues)에서 문제 해결 가이드를 참조하세요.

## <a name="supported-operating-systems"></a>지원되는 운영 체제

- **MMS 에이전트**: 지원되는 [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 및 [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 운영 체제를 검토합니다.
- **종속성 에이전트**: 지원되는 [Windows 및 Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) 운영 체제입니다.

## <a name="visualize-dependencies-for--1-hour"></a>1시간 넘게 종속성 시각화

에이전트 없는 종속성 분석을 사용하면 최대 30일 동안 맵에서 종속성을 시각화하거나 내보낼 수 있습니다.

에이전트 기반 종속성 분석을 사용할 경우 Azure Migrate에서는 지난 달의 특정 날짜로 돌아갈 수 있으며 종속성을 시각화할 수 있는 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 기간 기능을 사용해 어제의 종속성을 확인할 수는 있지만, 1시간 동안만 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용하여 더 긴 기간의 [종속성 데이터를 쿼리](./how-to-create-group-machine-dependencies.md)할 수 있습니다.

## <a name="visualized-dependencies-for--10-servers"></a>10대가 넘는 서버에 대해 종속성 시각화

Azure Migrate에서 에이전트 기반 종속성 분석을 사용하면 최대 10개의 VM이 있는 [그룹에 대한 종속성을 시각화](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)할 수 있습니다. 규모가 큰 그룹의 경우 종속성을 시각화하기 위해 VM을 더 작은 그룹으로 분할하는 것이 좋습니다.


## <a name="servers-show-install-agent"></a>서버에 "에이전트 설치" 표시

종속성 시각화를 사용하도록 설정하고 Azure에 서버를 마이그레이션한 후 서버는 다음과 같은 동작으로 인해 "종속성 보기" 대신 "에이전트 설치" 작업을 표시할 수 있습니다.

- Azure로 마이그레이션한 후 온-프레미스 서버는 꺼지고 동일한 VM이 Azure에서 작동됩니다. 이러한 서버는 다른 MAC 주소를 갖습니다.
- 서버는 온-프레미스 IP 주소를 보유했는지 여부에 따라 다른 IP 주소를 가질 수도 있습니다.
- MAC 주소와 IP 주소가 온-프레미스와 다른 경우 Azure Migrate는 온-프레미스 서버를 서비스 맵 종속성 데이터와 연결하지 않습니다. 이 경우 종속성 표시 옵션 대신 에이전트를 설치하는 옵션이 표시됩니다.
- Azure에 대해 테스트 마이그레이션을 수행한 후 온-프레미스 서버는 예상대로 켜진 상태를 유지합니다. Azure에서 작동되는 동급의 서버는 다른 MAC 주소를 획득하며 다른 IP 주소를 획득할 수도 있습니다. 이러한 서버에서 나가는 Azure Monitor 로그 트래픽을 차단하지 않는 한, Azure Migrate는 온-프레미스 서버를 서비스 맵 종속성 데이터와 연결하지 않으므로 종속성 표시 옵션 대신 에이전트를 설치하는 옵션이 표시됩니다.

## <a name="dependencies-export-csv-shows-unknown-process"></a>종속성 내보내기 CSV에 "알 수 없는 프로세스" 표시
에이전트 없는 종속성 분석에서 최선의 방식으로 프로세스 이름이 캡처됩니다. 특정 시나리오에서는 원본 및 대상 서버 이름과 대상 포트가 캡처되지만 종속성의 양쪽 끝에서 프로세스 이름을 확인하는 것은 불가능합니다. 이러한 경우 프로세스는 "알 수 없는 프로세스"로 표시됩니다.

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate"></a>내 Log Analytics 작업 영역은 Azure Migrate에서 작업 영역을 구성하려고 할 때 나열되지 않습니다.
Azure Migrate는 현재 미국 동부, 동남 아시아 및 서유럽 지역에서 OMS 작업 영역을 만들 수 있도록 지원합니다. 작업 영역을 다른 지역의 Azure Migrate 외부에서 만든 경우에도 현재 프로젝트에 연결할 수 없습니다.


## <a name="capture-network-traffic"></a>네트워크 트래픽 캡처

다음과 같이 네트워크 트래픽 로그를 수집합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. F12 키를 눌러 개발자 도구를 시작합니다. 필요한 경우 **탐색에 대한 항목 지우기** 설정을 선택 취소합니다.
3. **네트워크** 탭을 선택하고 네트워크 트래픽 캡처를 시작합니다.
   - 크롬에서 **Preserve log** 를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽을 캡처하고 있다는 뜻입니다. 빨간색 원이 나타나지 않으면 시작할 검은색 원을 선택합니다.
   - Microsoft Edge 및 Internet Explorer에서 기록이 자동으로 시작됩니다. 그렇지 않으면 녹색 재생 단추를 선택합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서는 마우스 오른쪽 단추로 클릭하고 **Save as HAR with content(콘텐츠와 함께 HAR로 저장)** 를 선택합니다. 그러면 로그가 .har 파일로 압축되어 내보내집니다.
   - Microsoft Edge 또는 Internet Explorer에서 **캡처된 트래픽 내보내기** 옵션을 선택합니다. 그러면 로그가 압축되어 내보내집니다.
6. **콘솔** 탭을 선택하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. **다른 이름으로 저장** 을 선택하여 로그를 내보내고 압축합니다.
   - Microsoft Edge 또는 Internet Explorer에서 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사** 를 선택합니다.
7. 개발자 도구를 닫습니다.


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>내 평가의 운영 체제 데이터는 어디에서 검색되나요?

- VMware VM의 경우 기본적으로 vCenter에서 제공하는 운영 체제 데이터입니다. 
   - VMware linux VM의 경우 애플리케이션 검색을 사용하도록 설정하면 게스트 VM에서 OS 세부 정보를 가져옵니다. 평가에서 OS 세부 정보를 확인하려면 검색된 서버 보기로 이동하여 "운영 체제" 열의 값 위에 마우스를 올려 놓습니다. 팝업되는 텍스트에서 표시되는 OS 데이터가 vCenter Server에서 또는 VM 자격 증명을 사용하여 게스트 VM에서 수집되었는지 확인할 수 있습니다. 
   - Windows VM의 경우 운영 체제 세부 정보는 항상 vCenter Server에서 가져옵니다.
- Hyper-V VM의 경우 운영 체제 데이터가 Hyper-V 호스트에서 수집됩니다.
- 물리적 서버의 경우 서버에서 가져옵니다.

## <a name="next-steps"></a>다음 단계

평가를 [만들거나](how-to-create-assessment.md) [사용자 지정](how-to-modify-assessment.md)합니다.