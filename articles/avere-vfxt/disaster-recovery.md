---
title: Azure용 Avere vFXT에 대한 재해 복구 지침
description: Avere vFXT에서 실수로 삭제 또는 중단으로부터 Azure용 데이터를 보호하는 방법
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 28278f76497d6e9d0fee221bb4ef32fe6d369db0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966657"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Azure용 Avere vFXT에 대한 재해 복구 지침

이 문서에서는 Azure 워크플로에 대한 Avere vFXT를 보호하기 위한 전략을 간략하게 설명하고 사고 또는 중단으로부터 복구할 수 있도록 데이터를 백업하기 위한 지침을 제공합니다.

Azure용 Avere vFXT는 일시적으로 데이터를 캐시에 저장합니다. 데이터는 온-프레미스 하드웨어 시스템, Azure Blob 저장소 컨테이너 또는 둘 다와 같은 백 엔드 저장소 시스템에 장기적으로 저장됩니다.

가동 중단 및 가능한 데이터 손실을 방지하려면 다음 네 가지 영역을 고려하십시오.

* Azure 시스템에 대한 Avere vFXT를 사용할 수 없는 경우 가동 중지 시간 방지
* 클러스터 캐시의 데이터 보호
* 백 엔드 NAS 하드웨어 스토리지의 데이터 보호
* 백 엔드 Azure Blob 클라우드 스토리지의 데이터 보호

Azure 고객에 대 한 모든 Avere vFXT 이러한 항목에 대 한 계획을 포함 하는 자체 포괄적인 재해 복구 계획을 만들어야 합니다. vFXT 클러스터에서 사용하는 응용 프로그램에 복원력을 구축할 수도 있습니다. 도움말을 보려면 [다음 단계의](#next-steps) 링크를 읽어보십시오.

## <a name="protect-against-downtime"></a>가동 중지 시간 으로부터 보호

중복성은 Azure 제품에 대한 Avere vFXT에 내장되어 있습니다.

* 클러스터는 가용성이 높으며 개별 클러스터 노드는 중단을 최소화하면서 장애 조치될 수 있습니다.
* 캐시에서 변경된 데이터는 장기 저장소를 위해 백 엔드 코어 파일러(하드웨어 NAS 또는 Azure Blob)에 정기적으로 기록됩니다.

Azure 클러스터의 각 Avere vFXT는 단일 가용성 영역에 있어야 하지만 지역 가동 중단 시 신속하게 액세스를 제공하기 위해 다른 영역 또는 다른 지역에 있는 중복 클러스터를 사용할 수 있습니다.

데이터에 대한 액세스 권한을 잃을 염려가 있는 경우 저장소 컨테이너를 여러 지역에 배치할 수도 있습니다. 그러나 리전 간 트랜잭션은 리전 내에 있는 트랜잭션보다 대기 시간이 높고 비용이 더 높습니다.

## <a name="protect-data-in-the-cluster-cache"></a>클러스터 캐시의 데이터 보호

캐시된 데이터는 항상 정기적인 종료 전에 핵심 파일러에 기록되지만 제어되지 않는 종료에서는 캐시의 변경된 데이터가 손실될 수 있습니다.

클러스터를 사용하여 파일 읽기만 최적화하는 경우 손실되는 변경 사항은 없습니다. 클러스터를 사용하여 파일 변경(쓰기)을 캐시하는 경우 핵심 파일러의 [캐시 정책을](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html) 조정할지 여부를 고려하십시오.<!-- link to legacy doc --> 을 사용하여 장기 저장소에 데이터가 기록되는 빈도를 사용자 지정할 수 있습니다.

일반적으로 복구 계획은 더 많은 데이터를 보유하고 일반적으로 실패 후 워크플로를 다시 설정하는 데 더 중요한 백 엔드 스토리지 시스템을 백업하는 데 중점을 두어야 합니다.

## <a name="protect-data-in-nas-core-filers"></a>NAS 코어 파일러의 데이터 보호

허용된 방법을 사용하여 NAS 공급자가 권장하는 스냅샷 및 전체 백업을 포함하여 온-프레미스 NAS 하드웨어 코어 파일러에 저장된 데이터를 보호합니다. 이러한 핵심 파일러에 대한 재해 복구는 이 문서의 범위를 벗어납니다.

## <a name="protect-data-in-azure-blob-storage"></a>Azure Blob 저장소의 데이터 보호

Azure용 Avere vFXT는 Azure Blob 코어 파일러에 로컬 중복 저장소(LRS)를 사용합니다. 즉, Blob 컨테이너의 데이터가 데이터 센터 내의 일시적인 하드웨어 오류에 대한 보호를 위해 자동으로 복사됩니다.

이 섹션에서는 드문 지역 전체 중단 또는 우발적인 삭제로부터 Blob 저장소의 데이터를 추가로 보호하는 방법에 대한 팁을 제공합니다.

Azure Blob 저장소의 데이터를 보호하는 모범 사례는 다음과 같습니다.

* 중요한 데이터를 다른 지역의 다른 저장소 계정에 자주 복사합니다(재해 복구 계획에 따라 결정되는 경우).
* 모든 대상 시스템의 데이터에 대한 액세스를 제어하여 실수로 삭제또는 손상되지 않도록 합니다. 데이터 저장소에 [리소스 잠금을](../azure-resource-manager/management/lock-resources.md) 사용하는 것이 좋습니다.
* Blob 코어 파일러에 대한 Azure [클라우드 스냅샷](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>) 기능에 대해 Avere vFXT를 사용하도록 설정합니다.

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Avere vFXT 코어 파일러 데이터를 백업 계정에 복사

다음 단계에 따라 다른 계정에서 데이터 백업을 설정합니다.

1. 필요한 경우 새 암호화 키를 생성하고 영향을 받는 시스템 외부에 안전하게 저장합니다.

   Azure 클러스터용 Avere vFXT에 의해 데이터가 암호화된 경우 데이터를 다른 저장소 계정에 복사하기 전에 새 암호화 키를 생성해야 합니다. 해당 키와 암호를 안전하고 지역 적 오류의 영향을 받지 않는 시설에 안전하게 저장합니다.

   컨테이너를 원래 클러스터에 다시 추가하는 경우에도 클러스터에 컨테이너를 추가할 때 이 키를 제공해야 합니다.

   [클라우드 암호화 설정](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>) 읽기<!-- link to legacy doc site --> 자세한 정보를 참조하십시오.

   컨테이너에서 Azure의 기본 제공 암호화만 사용하는 경우 이 단계를 건너뛸 수 있습니다.

1. 시스템에서 코어 파일러를 제거합니다. 이렇게 하면 클러스터가 변경된 모든 데이터를 백 엔드 저장소에 기록해야 합니다.

   백업 후 핵심 파일러를 다시 추가해야 하지만 백업을 제거하면 모든 데이터가 백 엔드에 완전히 기록되도록 하는 것이 가장 좋습니다. ("일시 중단" 옵션을 사용하면 변경된 데이터가 캐시에 남을 수 있습니다. <!-- xxx true? or just metadata? -->

   백업 후 컨테이너를 다시 추가할 때 복제할 수 있도록 핵심 파일러의 이름과 접합 정보(제어판의 **네임스페이스** 페이지에 나열됨)를 기록합니다.

   클러스터 제어판을 사용하여 핵심 파일러를 제거합니다. [클러스터 제어판을 열고](avere-vfxt-cluster-gui.md) **핵심 파일러** > **관리를 선택합니다.** 백업할 저장소 시스템을 찾아 **제거** 단추를 사용하여 클러스터에서 삭제합니다.

1. 다른 지역의 다른 저장소 계정에 빈 새 Blob 저장소 컨테이너를 만듭니다.

1. 편리한 복사 도구를 사용하여 핵심 파일러의 데이터를 새 컨테이너에 복사합니다. 복사본은 Azure용 Avere vFXT에서 사용하는 독점 클라우드 파일 시스템 형식을 변경하지 않고 변경 하지 않고 데이터를 복제해야 합니다. Azure 기반 도구에는 [AzCopy,](../storage/common/storage-use-azcopy-v10.md) [Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)및 [Azure 데이터 팩터리가](../data-factory/connector-azure-data-lake-store.md)포함됩니다.

1. 백업 컨테이너에 데이터를 복사한 후 [저장소 구성에](avere-vfxt-add-storage.md)설명된 대로 원래 컨테이너를 클러스터에 다시 추가합니다.

   * 클라이언트 워크플로를 변경할 필요가 없도록 동일한 핵심 파일러 이름 및 정션 정보를 사용합니다.
   * 버킷 내용 값을 기존 데이터 옵션으로 **설정합니다.**
   * 컨테이너가 클러스터에 의해 암호화된 경우 해당 내용에 대한 현재 암호화 키를 입력해야 합니다. (1단계에서 업데이트한 키입니다.)

첫 번째 백업 후 백업의 경우 새 저장소 컨테이너를 만들 필요가 없습니다. 그러나 백업을 수행할 때마다 새 암호화 키를 생성하여 현재 키가 기억되는 위치에 저장되어 있는지 확인하는 것이 좋습니다.

### <a name="access-a-backup-data-source-during-an-outage"></a>가동 중단 중에 백업 데이터 원본에 액세스

Azure 클러스터용 Avere vFXT에서 백업 컨테이너에 액세스하려면 다음 프로세스를 따르십시오.

1. 필요한 경우 영향을 받지 않는 지역에서 Azure 클러스터에 대한 새 Avere vFXT를 만듭니다.

   > [!TIP]
   > Azure 클러스터에 대한 Avere vFXT를 만들 때 생성 템플릿 및 매개 변수의 복사본을 저장할 수 있습니다. 기본 클러스터를 만들 때 이 정보를 저장하는 경우 이 정보를 사용하여 동일한 속성을 가진 대체 클러스터를 만들 수 있습니다. [요약](avere-vfxt-deploy.md#validation-and-purchase) 페이지에서 템플릿 다운로드 **및 매개변수 링크를 클릭합니다.** 클러스터를 만들기 전에 정보를 파일에 저장합니다.

1. 중복 Blob 컨테이너를 가리키는 새 클라우드 코어 파일러를 추가합니다.

   대상 컨테이너에 핵심 파일러 생성 마법사의 **버킷 내용** 설정에 데이터가 이미 포함되어 있는지 지정해야 합니다. (실수로 이 설정을 **Empty로**두면 시스템에서 경고를 해야 합니다.)  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 필요한 경우 클라이언트를 업데이트하여 원본 대신 새 클러스터 또는 새 코어 파일러를 탑재합니다. 원래 컨테이너와 동일한 이름 및 정션 경로를 가진 대체 코어 파일러를 추가하는 경우 새 IP 주소에 새 클러스터를 탑재해야 하는 경우가 아니면 클라이언트 프로세스를 업데이트할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

* Azure용 Avere vFXT에 대한 사용자 지정 설정에 대한 자세한 내용은 [클러스터 튜닝](avere-vfxt-tuning.md)을 참조하십시오.
* Azure에서 재해 복구 및 복원력 있는 응용 프로그램 빌드에 대해 자세히 알아봅니다.

  * [Azure 복구력 기술 지침](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
  * [지역 전체의 서비스 중단으로부터 복구](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure 애플리케이션에 대한 재해 복구 및 고가용성](<https://docs.microsoft.com/azure/resiliency/resiliency-disaster-recovery-high-availability-azure-applications>)
  <!-- can't find these in the source tree to use relative links -->
