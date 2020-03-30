---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 53bbee6dd75e045c2a7e95c88a0138c9859d12db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80374098"
---
이 문서에서는 Azure Managed Disks 및 Azure 프리미엄 SD 디스크에 대해 몇몇 자주 묻는 질문과 대답을 설명합니다.

## <a name="managed-disks"></a>Managed Disks

**Azure Managed Disks란?**

Managed Disks는 스토리지 계정 관리를 처리하여 Azure IaaS VM을 위한 디스크 관리를 간소화하는 기능입니다. 자세한 내용은 [Managed Disks 개요](../articles/virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

**80GB인 기존 VHD에서 표준 Managed Disk를 만들 경우 비용은 얼마나 드나요?**

80GB VHD로 만든 표준 관리 디스크는 다음과 같은 사용 가능 표준 디스크 크기인 S10 디스크로 취급됩니다. S10 디스크 가격 책정에 따라 요금이 부과됩니다. 자세한 내용은 가격 [책정 페이지를](https://azure.microsoft.com/pricing/details/storage)참조하십시오.

**표준 관리 디스크에 대한 트랜잭션 비용이 있나요?**

예. 각 트랜잭션에 대해 요금이 부과됩니다. 자세한 내용은 가격 [책정 페이지를](https://azure.microsoft.com/pricing/details/storage)참조하십시오.

**표준 관리 디스크에 대한 요금은 디스크에 있는 데이터의 실제 크기에 대해 부과되나요? 아니면 디스크의 프로비전된 용량에 대해 부과되나요?**

디스크의 프로비전된 용량에 따라 청구됩니다. 자세한 내용은 가격 [책정 페이지를](https://azure.microsoft.com/pricing/details/storage)참조하십시오.

**프리미엄 Managed Disks의 가격은 관리되지 않는 디스크와 어떻게 다르게 책정되나요?**

프리미엄 관리 디스크의 가격은 관리되지 않는 프리미엄 디스크와 같습니다.

**내 Managed Disks의 스토리지 계정 유형(표준 또는 프리미엄)을 내가 변경할 수 있나요?**

예. Azure Portal, PowerShell 또는 Azure CLI를 사용하여 Managed Disks의 스토리지 계정 형식을 변경할 수 있습니다.

**Azure Storage 계정에서 VHD 파일을 사용하여 다른 구독에 Managed Disk를 만들 수 있나요?**

예.

**Azure Storage 계정에서 VHD 파일을 사용하여 다른 지역에 관리 디스크를 만들 수 있나요?**

아니요.

**고객이 Managed Disks를 사용하는 경우 규모 제한이 있나요?**

Managed Disks는 스토리지 계정과 관련된 한도를 없앱니다. 그러나 최대 한도는 하나의 구독에 대해 지역당 및 디스크 형식당 50,000개의 관리 디스크입니다.

**관리 및 관리되지 않는 디스크를 조합하여 가용성 집합의 VM을 구성할 수 있나요?**

아니요. 가용성 집합에 있는 VM은 모두 Managed Disks를 사용하거나 모두 관리되지 않는 디스크를 사용해야 합니다. 가용성 집합을 만들 때 사용할 디스크 유형을 사용자가 선택할 수 있습니다.

**Managed Disks가 Azure Portal에서 기본 옵션인가요?**

예.

**내가 빈 관리 디스크를 만들 수 있나요?**

예. 사용자가 빈 디스크를 만들 수 있습니다. Managed Disk는 VM에 독립적으로 즉, VM에 연결하지 않고 만들 수 있습니다.

**Managed Disks를 사용하는 가용성 집합에 지원되는 장애 도메인 수는 몇 개인가요?**

Managed Disks를 사용하는 가용성 집합이 위치한 지역에 따라 지원되는 장애 도메인 수는 2개 또는 3개입니다.

**진단을 위한 표준 스토리지 계정은 어떤 방식으로 설정되나요?**

VM 진단을 위한 프라이빗 스토리지 계정을 설정할 수 있습니다.

**어떤 종류의 역할 기반 Access Control 지원을 Managed Disks에 사용할 수 있나요?**

Managed Disks에서는 세 가지 주요 기본 역할을 지원합니다.

* 소유자: 액세스를 포함한 모든 것을 관리할 수 있음
* 참여자: 액세스를 제외한 모든 것을 관리할 수 있음
* 읽기 권한자: 모든 항목을 볼 수 있지만 변경할 수 없음

**프라이빗 스토리지 계정에 Managed Disk를 복사하거나 내보낼 수 있는 방법이 있나요?**

관리 디스크에 대한 읽기 전용 SAS(공유 액세스 서명) URI를 생성한 후 이를 사용하여 프라이빗 스토리지 계정 또는 온-프레미스 스토리지에 콘텐츠를 복사할 수 있습니다. Azure 포털, Azure PowerShell, Azure CLI 또는 [AzCopy를](../articles/storage/common/storage-use-azcopy.md) 사용하여 SAS URI를 사용할 수 있습니다.

**내 관리 디스크의 복사본을 만들 수 있나요?**

사용자는 관리 디스크의 스냅샷을 만든 다음, 이 스냅샷을 사용하여 다른 관리 디스크를 만들 수 있습니다.

**관리되지 않는 디스크도 계속 지원되나요?**

예. 관리되지 않는 디스크와 Managed Disks가 모두 지원됩니다. 새 워크로드에 대해 Managed Disks를 사용하고 현재 워크로드를 Managed Disks로 마이그레이션하는 것이 좋습니다.

**비관리 및 관리 디스크를 동일한 VM에 함께 배치할 수 있나요?**

아니요.

**128GB 디스크를 만든 다음 크기를 130 giB(GiB)로 늘리면 다음 디스크 크기(256 GiB)에 대한 요금이 청구되나요?**

예.

**로컬 중복 스토리지, 지역 중복 스토리지 및 영역 중복 스토리지 Managed Disks를 만들 수 있나요?**

Azure Managed Disks에서는 현재 로컬 중복 스토리지 Managed Disks만 지원합니다.

**Managed Disks를 축소하거나 크기를 줄일 수 있나요?**

아니요. 이 기능은 현재 지원되지 않습니다.

**내 디스크에서 임대를 중단할 수 있나요?**

아니요. 현재 디스크를 사용하는 경우 실수로 삭제하지 않도록 방지하기 위해 임대로는 지원되지 않습니다.

**VM을 프로비전하는 데 특수화된(시스템 준비 도구를 사용하여 생성되거나 일반화된) 운영 체제 디스크를 사용하는 경우 컴퓨터 이름 속성을 변경할 수 있나요?**

아니요. 컴퓨터 이름 속성은 업데이트할 수 없습니다. 새 VM은 운영 체제 디스크를 만들 때 사용한 부모 VM에서 이 속성을 상속합니다. 

**Managed Disks를 사용하여 VM을 만드는 Azure Resource Manager 템플릿 예제를 어디에 배치할 수 있나요?**
* [Managed Disks를 사용하는 템플릿 목록](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* https://github.com/chagarw/MDPP

**BLOB에서 디스크를 만들 때 해당 원본 BLOB와 지속적으로 유지되는 관계가 있습니까?**

아니요. 새로 생성되는 디스크는 그 당시의 완전 독립형 BLOB 복사본이며, 둘 사이에는 연결 관계가 없습니다. 원할 경우 디스크를 만들면 새로 생성되는 디스크에 어떤 식으로든 영향을 주지 않고 원본 BLOB가 삭제될 수 있습니다.

**관리 또는 비관리 디스크가 생성된 후 이름을 변경할 수 있습니까?**

관리 디스크의 이름은 변경할 수 없습니다. 하지만 현재 VHD 또는 VM에 연결되어 있지 않은 경우에 한해 비관리 디스크의 이름은 변경할 수 있습니다.

**Azure 디스크에서 GPT 분할을 사용할 수 있나요?**

1세대 이미지는 OS 디스크가 아닌 데이터 디스크에서만 GPT 분할을 사용할 수 있습니다. OS 디스크는 MBR 파티션 스타일을 사용해야 합니다.

[2세대 이미지는](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2) 데이터 디스크뿐만 아니라 OS 디스크에서도 GPT 분할을 사용할 수 있습니다.

**스냅숏을 지원하는 디스크 유형은 무엇입니까?**

프리미엄 SSD, 표준 SSD 및 표준 HDD 지원 스냅샷. 이러한 세 가지 디스크 유형의 경우 모든 디스크 크기(최대 32TiB 크기의 디스크 포함)에 대해 스냅숏이 지원됩니다. 울트라 디스크는 스냅샷을 지원하지 않습니다.

**Azure 디스크 예약이란 무엇입니까?**
디스크 예약은 1년 전에 디스크 저장소를 미리 구매할 수 있는 옵션으로 총 비용을 절감할 수 있습니다. Azure 디스크 예약에 대한 자세한 내용은 Azure [Disk에 예약 할인이 적용되는 방식을 이해한다는](../articles/cost-management-billing/reservations/understand-disk-reservations.md)주제에 대한 도움말을 참조하십시오.

**Azure 디스크 예약은 어떤 옵션을 제공합니까?**    
Azure 디스크 예약은 지정된 SSD에서 P30(1 TiB)에서 P80(32TiB)까지 1년 동안 프리미엄 SSD를 구매할 수 있는 옵션을 제공합니다. 디스크 예약을 구입하는 데 필요한 최소 디스크 수에는 제한이 없습니다. 또한 단일 선결제 또는 월별 결제로 결제하도록 선택할 수 있습니다. 프리미엄 SSD 관리 디스크에는 추가 트랜잭션 비용이 적용되지 않습니다.    

예약은 용량이 아닌 디스크 형태로 이루어집니다. 즉, P80(32 TiB) 디스크를 예약하면 단일 P80 디스크를 얻게 된 다음 특정 예약을 두 개의 더 작은 P70(16TiB) 디스크로 나눌 수 없습니다. 물론 두 개의 별도의 P70(16TiB) 디스크를 포함하여 원하는 만큼 디스크를 예약할 수 있습니다.

**Azure 디스크 예약은 어떻게 적용됩니다.**    
디스크 예약은 VM(예약된 가상 시스템) 인스턴스와 유사한 모델을 따릅니다. 디스크 예약이 다른 SCO에 적용할 수 없다는 차이점이지만 VM 인스턴스는 적용할 수 있습니다. VM 인스턴스에 대한 자세한 내용은 [Azure 예약 VM 인스턴스를 통해 비용 절감을](../articles/virtual-machines/linux/prepay-reserved-vm-instances.md) 참조하세요.     

**여러 지역에서 Azure 디스크 예약을 통해 구입한 데이터 저장소를 사용할 수 있습니까?**    
Azure 디스크 예약은 특정 지역 및 SKU(예: 미국 동부 2의 P30)에 대해 구입되므로 이러한 구문 외부에서사용할 수 없습니다. 다른 지역 또는 SCO에서 디스크 저장소 요구 사항에 대한 추가 Azure 디스크 예약을 언제든지 구입할 수 있습니다.    

**Azure 디스크 예약이 만료되면 어떻게 되나요?**    
만료 30일 전부터 만료일에 다시 이메일 알림을 받게 됩니다. 예약이 만료되면 배포된 디스크가 계속 실행되며 최신 [종량제 요금이](https://azure.microsoft.com/pricing/details/managed-disks/)청구됩니다.

### <a name="azure-shared-disks"></a>Azure 공유 디스크

**관리되지 않는 디스크 또는 페이지 Blob에서 공유 디스크 기능이 지원되고 있습니까?**

아니요, 프리미엄 SSD 관리 디스크에만 지원됩니다.

**공유 디스크를 지원하는 지역은 무엇입니까?**

현재 미국 중서부에만 있습니다.

**공유 디스크를 OS 디스크로 사용할 수 있습니까?**

아니요, 공유 디스크는 데이터 디스크에 대해서만 지원됩니다.

**공유 디스크를 지원하는 디스크 크기는 무엇입니까?**

P15 이상 지원 디스크를 지원하는 프리미엄 SSD만 가능합니다.

**기존 프리미엄 SSD가 있는 경우 공유 디스크를 활성화할 수 있습니까?**

API 버전 2019-07-01 이상으로 만든 모든 관리 형 디스크는 공유 디스크를 활성화할 수 있습니다. 이렇게 하려면 연결된 모든 VM에서 디스크마운트를 해제해야 합니다. 그런 다음 디스크의 `maxShares` 속성을 편집합니다.

**공유 모드에서 디스크를 더 이상 사용하지 않으려면 어떻게 비활성화합니까?**

연결된 모든 VM에서 디스크마운트를 해제합니다. 그런 다음 디스크의 maxShare 속성을 1로 편집합니다.

**공유 디스크의 크기를 조정할 수 있습니까?**

예.

**공유 디스크를 사용하도록 설정한 디스크에서 쓰기 가속기를 활성화할 수 있습니까?**

아니요.

**공유 디스크가 활성화된 디스크에 대해 호스트 캐싱을 활성화할 수 있습니까?**

지원되는 유일한 호스트 캐싱 옵션은 '없음'입니다.

## <a name="ultra-disks"></a>Ultra disks

**울트라 디스크 처리량을 어떻게 설정해야 합니까?**
디스크 처리량을 어떻게 설정해야 할지 잘 모르는 경우 먼저 IO 크기가 16 KiB라고 가정하고 응용 프로그램을 모니터링할 때 거기에서 성능을 조정하는 것이 좋습니다. 수식은 다음과 같은: MBps의 처리량 = IOPS * 16 /1000의 #.

**디스크를 40000 IOPS로 구성했지만 12800 IOPS만 표시되고 있는데 디스크성능이 표시되지 않는 이유는 무엇입니까?**
디스크 스로틀 외에도 VM 수준에서 부과되는 IO 스로틀이 있습니다. 사용 중인 VM 크기가 디스크에 구성된 수준을 지원할 수 있는지 확인하십시오. VM에서 부과하는 IO 제한에 대한 자세한 내용은 [Azure의 Windows 가상 컴퓨터 의 크기를](../articles/virtual-machines/windows/sizes.md)참조하십시오.

**울트라 디스크로 캐싱 레벨을 사용할 수 있습니까?**
아니요, 울트라 디스크는 다른 디스크 유형에서 지원되는 다른 캐싱 방법을 지원하지 않습니다. 디스크 캐싱을 없음으로 설정합니다.

**기존 VM에 울트라 디스크를 연결할 수 있습니까?**
VM은 Ultra 디스크를 지원하는 리전 및 가용성 영역 쌍에 있어야 합니다. 자세한 내용은 [울트라 디스크 시작](../articles/virtual-machines/windows/disks-enable-ultra-ssd.md) 을 참조하십시오.

**울트라 디스크를 VM의 OS 디스크로 사용할 수 있습니까?**
아니요, 울트라 디스크는 데이터 디스크로만 지원되며 4K 네이티브 디스크로만 지원됩니다.

**기존 디스크를 울트라 디스크로 변환할 수 있습니까?**
아니요, 하지만 기존 디스크에서 울트라 디스크로 데이터를 마이그레이션할 수 있습니다. 기존 디스크를 울트라 디스크로 마이그레이션하려면 두 디스크를 동일한 VM에 연결하고 디스크 데이터를 한 디스크에서 다른 디스크로 복사하거나 데이터 마이그레이션을 위한 제3자 솔루션을 활용합니다.

**울트라 디스크에 대한 스냅샷을 만들 수 있습니까?**
아니요, 스냅샷은 아직 사용할 수 없습니다.

**Azure 백업은 울트라 디스크에 사용할 수 있습니까?**
아니요, Azure 백업 지원을 아직 사용할 수 없습니다.

**가용성 집합에서 실행 중인 VM에 울트라 디스크를 연결할 수 있습니까?**
아니요, 아직 지원되지 않습니다.

**울트라 디스크를 사용하여 VM에 대한 Azure 사이트 복구를 활성화할 수 있습니까?**
아니요, Azure 사이트 복구는 아직 울트라 디스크에 대해 지원되지 않습니다.

## <a name="uploading-to-a-managed-disk"></a>관리 디스크에 업로드

**기존 관리 디스크에 데이터를 업로드할 수 있습니까?**

아니요, **ReadyToUpload** 상태가 있는 새 빈 디스크를 만드는 동안에만 업로드를 사용할 수 있습니다.

**관리 되는 디스크에 업로드 하려면 어떻게 해야 합니까?**

createOption 속성 의 [create를](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#diskcreateoption) 사용하여 관리 디스크를 [만듭니다데이터](https://docs.microsoft.com/rest/api/compute/disks/createorupdate#creationdata) "업로드"로 설정, 당신은 그것에 데이터를 업로드 할 수 있습니다.

**업로드 상태인 동안 VM에 디스크를 연결할 수 있습니까?**

아니요.

**업로드 상태에서 맨데쉬운 디스크의 스냅샷을 만들 수 있습니까?**

아니요.

## <a name="standard-ssd-disks"></a>표준 SSD 디스크

**Azure 표준 SSD 디스크란?**
표준 SSD 디스크는 반도체 미디어에서 지원되며 더 낮은 IOPS 수준에서 일관된 성능을 필요로 하는 워크로드에 대해 비용 효율적인 스토리지로 최적화된 표준 디스크입니다.

<a id="standard-ssds-azure-regions"></a>**현재 표준 SSD 디스크가 지원되는 지역은 어디입니까?**
이제 모든 Azure 지역에서 표준 SSD 디스크를 지원합니다.

**표준 SSD를 사용하는 경우 Azure Backup을 사용할 수 있나요?**
예. 이제 Azure Backup을 사용할 수 있습니다.

**표준 SSD 디스크를 만드는 방법**
Azure 리소스 관리자 템플릿, SDK, PowerShell 또는 CLI를 사용하여 표준 SSD 디스크를 만들 수 있습니다. 다음은 Resource Manager 템플릿에서 표준 SSD 디스크를 만드는 데 필요한 매개 변수입니다.

* Microsoft.Compute에 대한 *apiVersion*은 `2018-04-01` 이상으로 설정해야 합니다.
* *managedDisk.storageAccountType*을 `StandardSSD_LRS`로 지정합니다.

다음 예제에서는 표준 SSD 디스크를 사용하는 VM에 대한 *properties.storageProfile.osDisk* 섹션을 보여 줍니다.

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

템플릿을 사용하여 표준 SSD 디스크를 만드는 방법에 대한 전체 템플릿 예제를 보려면 [표준 SSD 데이터 디스크를 사용하여 Windows 이미지에서 VM 만들기](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)를 참조하세요.

**기존 디스크를 표준 SSD로 변환할 수 있나요?**
 예, 할 수 있습니다. Managed Disks로 변환하는 방법에 대한 일반 지침을 보려면 [Azure 관리 디스크 스토리지를 표준에서 프리미엄으로, 또 그 반대로 변환](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage)을 참조하세요. 또한 다음 값을 사용하여 디스크 유형을 표준 SSD로 업데이트하세요.
-AccountType StandardSSD_LRS

**HDD 대신 표준 SSD 디스크를 사용할 경우의 이점은 무엇입니까?**
표준 SSD 디스크는 HDD 디스크에 비해 더 나은 대기 시간, 일관성, 가용성 및 안정성을 제공합니다. 따라서 애플리케이션 워크로드가 표준 SSD에서 훨씬 더 원활하게 실행됩니다. 프리미엄 SSD 디스크는 가장 IO 사용량이 많은 프로덕션 워크로드에 권장되는 솔루션입니다.

**표준 SSD를 관리되지 않는 디스크로 사용할 수 있나요?**
아니요. 표준 SSD 디스크는 Managed Disks로만 사용할 수 있습니다.

**표준 SSD 디스크가 "단일 인스턴스 VM SLA"를 지원하나요?**
아니요. 표준 SSD에는 단일 인스턴스 VM SLA가 없습니다. 단일 인스턴스 VM SLA에는 프리미엄 SSD 디스크를 사용합니다.

## <a name="migrate-to-managed-disks"></a>Managed Disks로 마이그레이션

**마이그레이션이 Managed Disks 성능에 영향을 주나요?**

마이그레이션에는 스토리지 위치 간 디스크 이동이 포함됩니다. 이는 데이터의 백그라운드 복사본을 통해 오케스트레이션되며, 디스크의 데이터 양에 따라 일반적으로 24시간 미만의 완료하는 데 몇 시간이 걸릴 수 있습니다. 이 기간 동안 일부 읽기가 원래 위치로 리디렉션될 수 있어 완료하는 데 더 오래 걸릴 수 있으므로 애플리케이션의 읽기 대기 시간이 일반적인 읽기 대기 시간보다 길어질 수 있습니다. 이 기간 동안 쓰기 대기 시간에는 영향이 없습니다.  

**Managed Disks로 마이그레이션하기 전/후에 기존 Azure Backup 서비스 구성에 어떤 변경이 필요한가요?**

변경할 필요가 없습니다.

**마이그레이션 전에 Azure Backup 서비스를 통해 만든 내 VM 백업은 계속 작동하나요?**

예. 백업은 원활하게 작동합니다.

**Managed Disks로 마이그레이션하기 전/후에 기존 Azure Disk Encryption 구성에 어떤 변경이 필요한가요?**

변경할 필요가 없습니다.

**관리되지 않는 디스크에서 관리되는 디스크로 설정된 기존 가상 시스템 규모의 자동 마이그레이션이 지원되고 있습니까?**

아니요. 관리되지 않는 디스크에서 이전 확장 집합의 이미지를 사용하여 Managed Disks로 새 확장 집합을 만들 수 있습니다.

**Managed Disks로 마이그레이션하기 전에 페이지 Blob 스냅샷에서 Managed Disks를 만들 수 있나요?**

아니요. 페이지 blob 스냅샷을 페이지 blob으로 내보내고 내보낸 페이지 blob에서 Managed Disks를 만들 수 있습니다.

**Azure Site Recovery에 의해 보호되는 온-프레미스 컴퓨터를 Managed Disks가 있는 VM으로 장애 조치(Failover)할 수 있나요?**

예. Managed Disks가 있는 VM으로 장애 조치(Failover)하도록 선택할 수 있습니다.

**마이그레이션을 수행할 경우 Azure 간 복제를 통해 Azure Site Recovery에 의해 보호되는 Azure VM에 영향을 미치나요?**

아니요. 관리되는 디스크를 사용하여 VM에 대한 Azure 사이트 복구 Azure에서 Azure로 보호할 수 있습니다.

**Managed Disks에 이전에 암호화된 스토리지 계정에 있는 관리되지 않는 디스크가 있는 VM을 마이그레이션할 수 있나요?**

yes

## <a name="managed-disks-and-storage-service-encryption"></a>Managed Disks 및 Storage 서비스 암호화

**Managed Disk를 만들 경우 Azure Storage 서비스 암호화를 기본적으로 사용하나요?**

예.

**부팅 볼륨이 관리 디스크에서 기본적으로 암호화되어 있습니까?**

예. 기본적으로 모든 관리되는 디스크는 OS 디스크를 포함하여 암호화됩니다.

**암호화 키는 누가 관리하나요?**

Microsoft에서 암호화 키를 관리합니다.

**내 Managed Disks에 Storage 서비스 암호화를 비활성화할 수 있나요?**

아니요.

**Storage 서비스 암호화는 특정 지역에서만 사용할 수 있나요?**

아니요. Managed Disks를 사용할 수 있는 모든 지역에서 사용할 수 있습니다. 모든 공용 지역 및 독일에서 Managed Disks를 사용할 수 있습니다. 중국에서도 사용할 수 있지만, 고객 관리 키가 아닌 Microsoft 관리 키의 경우로 국한됩니다.

**내 Managed Disk가 암호화되었는지 어떻게 확인할 수 있나요?**

Azure Portal, Azure CLI 및 PowerShell에서 Managed Disk를 만든 시간을 알아볼 수 있습니다. 2017년 6월 9일 이후인 경우 디스크는 암호화됩니다.

**2017년 6월 10일 이전에 만들어진 내 기존 디스크를 어떻게 암호화할 수 있나요?**

2017년 6월 10일을 기준으로 기존 Managed Disks에 작성된 새 데이터는 자동으로 암호화됩니다. 기존 데이터를 암호화할 예정이며 암호화는 백그라운드에서 비동기적으로 수행됩니다. 이제 기존 데이터를 암호화해야 하는 경우 디스크의 복사본을 만듭니다. 새 디스크가 암호화됩니다.

* [Azure CLI를 사용하여 Managed Disks 복사](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)
* [PowerShell을 사용하여 Managed Disks 복사](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json)

**관리되는 스냅샷 및 이미지가 암호화되나요?**

예. 2017년 6월 9일 이후에 만든 모든 관리되는 스냅샷 및 이미지는 자동으로 암호화됩니다. 

**Managed Disks에 이전에 암호화된 스토리지 계정에 있는 관리되지 않는 디스크가 있는 VM을 변환할 수 있나요?**

yes

**Managed Disk 또는 스냅샷에서 내보낸 VHD도 암호화되나요?**

아니요. 하지만 암호화된 Managed Disk 또는 스냅샷의 암호화된 스토리지 계정에 VHD를 내보낼 경우 암호화됩니다. 

## <a name="premium-disks-managed-and-unmanaged"></a>프리미엄 디스크: 관리 및 관리되지 않는 디스크

**VM에서 DSv2와 같이 프리미엄 SD 디스크를 지원하는 크기를 사용하는 경우 프리미엄 및 표준 데이터 디스크를 모두 연결할 수 있나요?** 

예.

**프리미엄 및 표준 데이터 디스크를 모두 D, Dv2, G 또는 F 시리즈와 같이 프리미엄 SD 디스크를 지원하지 않는 크기에 연결할 수 있나요?**

아니요. 프리미엄 SD 디스크를 지원하는 크기를 사용하지 않는 VM에는 표준 데이터 디스크만 연결할 수 있습니다.

**80GB인 기존 VHD로 프리미엄 데이터 디스크를 만들 경우 비용은 얼마가 드나요?**

80GB VHD에서 만든 프리미엄 데이터 디스크는 그 다음 프리미엄 디스크 크기인 P10으로 취급됩니다. P10 디스크 가격 책정에 따라 요금이 부과됩니다.

**프리미엄 SD 디스크를 사용하면 트랜잭션 비용이 있나요?**

특정 한도의 IOPS 및 처리량이 프로비전되는 디스크 크기마다 고정 비용이 있습니다. 기타 비용은 아웃바운드 대역폭 및 스냅샷 용량입니다(해당하는 경우). 자세한 내용은 가격 [책정 페이지를](https://azure.microsoft.com/pricing/details/storage)참조하십시오.

**디스크 캐시에서 가져올 수 있는 IOPS 및 처리량 한도는 얼마나 되나요?**

DS 시리즈의 캐시 및 로컬 SSD에 대한 결합 제한은 코어당 4,000 IOPS 및 코어당 초당 33MiB입니다. GS 시리즈는 코어당 5,000 IOPS 및 코어당 초당 50MiB를 제공합니다.

**Managed Disks VM에 로컬 SSD가 지원되나요?**

로컬 SSD는 Managed Disks VM에 포함되어 있는 임시 스토리지입니다. 이 임시 스토리지에 대한 추가 비용은 없습니다. 이 로컬 SSD가 Azure Blob Storage에 보존되지 않기 때문에 애플리케이션 데이터를 저장하는 데 사용하지 않는 것이 좋습니다.

**프리미엄 디스크에서 TRIM의 사용에 대한 영향이 있나요?**

프리미엄 또는 표준 디스크의 Azure 디스크에서 TRIM을 사용해도 문제는 없습니다.

## <a name="new-disk-sizes-managed-and-unmanaged"></a>새 디스크 크기: 관리 및 관리되지 않는 디스크

**적용 가능한 프리미엄 SSD 디스크 크기에 대한 버스팅 기능을 지원하는 지역은 무엇입니까?**

버스팅 기능은 현재 미국 Azure 서부에서 지원됩니다.

**어떤 지역에서 지원되는 4/8/16 GiB 관리 디스크 크기(P1/P2/P3, E1/E2/E3)는 지원됩니까?**

이러한 새 디스크 크기는 현재 미국 Azure 서부에서 지원됩니다.

**관리되지 않는 디스크 또는 페이지 Blob에서 P1/P2/P3 디스크 크기를 지원합니까?**

아니요, 프리미엄 SSD 관리 디스크에서만 지원됩니다. 

**관리되지 않는 디스크 또는 페이지 Blob에서 E1/E2/E3 디스크 크기가 지원되나요?**

아니요, 모든 크기의 표준 SSD 관리 디스크는 관리되지 않는 디스크 또는 페이지 Blob과 함께 사용할 수 없습니다.

**운영 체제 및 데이터 디스크에 지원되는 가장 큰 관리 디스크 크기는 어떻게 되나요?**

Azure에서 운영 체제 디스크에 지원하는 파티션 형식은 MBR(마스터 부트 레코드)입니다. MBR 형식은 최대 2TiB의 디스크 크기를 지원합니다. Azure에서 운영 체제 디스크에 지원하는 최대 크기는 2TiB입니다. Azure는 관리 데이터 디스크에 최대 32TiB를 지원합니다.

**운영 체제 및 데이터 디스크에 지원되는 가장 큰 관리되지 않는 디스크 크기는 어떻게 되나요?**

Azure에서 운영 체제 디스크에 지원하는 파티션 형식은 MBR(마스터 부트 레코드)입니다. MBR 형식은 최대 2TiB의 디스크 크기를 지원합니다. Azure에서 운영 체제 디스크에 지원하는 최대 관리되지 않는 디스크 크기는 2TiB입니다. Azure는 관리되지 않는 디스크에 최대 4TiB를 지원합니다.

**지원되는 가장 큰 페이지 Blob 크기는 무엇인가요?**

Azure에서 지원하는 페이지 Blob 크기는 8TiB(8,191GiB)입니다. VM에 데이터 또는 운영 체제 디스크로 연결될 경우의 최대 페이지 Blob 크기는 4TiB(4,095GiB)입니다.

**1TiB를 초과하는 디스크를 생성, 연결, 크기 조정 및 업로드하려면 새 버전의 Azure 도구를 사용해야 하나요?**

1TiB를 초과하는 디스크를 생성, 연결 또는 크기 조정하기 위해 기존 Azure 도구를 업그레이드할 필요가 없습니다. 페이지 Blob 또는 관리되지 않는 디스크로 온-프레미스에서 Azure에 직접 VHD 파일을 업로드하려면 아래에 나열된 최신 도구 집합을 사용해야 합니다. 최대 8TiB의 VHD 업로드만 지원됩니다.

|Azure 도구      | 지원되는 버전                                |
|-----------------|---------------------------------------------------|
|Azure PowerShell | 버전 번호 4.1.0: 2017년 6월 릴리스 이상|
|Azure CLI v1     | 버전 번호 0.10.13: 2017년 5월 릴리스 이상|
|Azure CLI v2     | 버전 번호 2.0.12: 2017년 7월 릴리스 이상|
|AzCopy              | 버전 번호 6.1.0: 2017년 6월 릴리스 이상|

**P4 및 P6 디스크 크기가 관리되지 않는 디스크 또는 페이지 Blob에 지원되나요?**

P4(32GiB) 및 P6(64GiB) 디스크 크기는 관리되지 않는 디스크 및 페이지 Blob에 대한 기본 디스크 계층으로 지원되지 않습니다. 디스크를 이러한 계층에 매핑하려면 P4 및 P6로 명시적으로 [Blob 계층을 설정](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)해야 합니다. Blob 계층을 설정하지 않고 디스크 크기 또는 콘텐츠 길이가 32GiB 미만이거나 32GiB에서 64GiB 사이인 관리되지 않는 디스크 또는 페이지 Blob를 배포하는 경우 500IOPS 및 100MiB/초, 매핑된 가격 책정 계층을 포함하는 P10에 계속해서 이르게 됩니다.

**작은 디스크를 사용하도록 설정하기 전에 64GiB 미만인 기존 프리미엄 관리 디스크를 만든 경우(2017년 6월 15일경) 어떻게 청구되나요?**

64GiB 미만인 기존의 작은 프리미엄 디스크는 P10 가격 책정 계층에 따라 계속 청구됩니다.

**64GiB 미만의 작은 프리미엄 디스크의 디스크 계층을 P10에서 P4 또는 P6으로 전환하려면 어떻게 해야 하나요?**

작은 디스크의 스냅샷을 찍고 디스크를 만들어서 프로비전된 크기에 따라 P4 또는 P6으로 가격 책정 계층을 자동으로 전환할 수 있습니다.

**기존 관리형 디스크의 크기를 4테비바이트(TiB) 미만의 크기에서 새로 도입된 디스크 크기로 최대 32TiB로 조정할 수 있습니까?**

예.

**Azure 백업 및 Azure 사이트 복구 서비스에서 지원하는 가장 큰 디스크 크기는 무엇입니까?**

Azure Backup에서 지원하는 가장 큰 디스크 크기는 32 TiB(암호화된 디스크의 경우 4TiB)입니다. Azure 사이트 복구에서 지원하는 가장 큰 디스크 크기는 8TiB입니다. Azure 사이트 복구에서는 최대 32TiB의 더 큰 디스크에 대한 지원을 아직 사용할 수 없습니다.

**최적화된 디스크 IOPS 및 대역폭을 달성하기 위해 표준 SSD 및 표준 HDD 디스크에 대해 더 큰 디스크 크기(>4 TiB)에 권장되는 VM 크기는 무엇입니까?**

표준 SSD 및 표준 HDD 대용량 디스크 크기(>4 TiB)의 디스크 처리량을 500 IOPS 및 60MiB/s를 초과하는 경우 B 시리즈, DSv2 시리즈, Dsv3 시리즈, ESv3 시리즈, FS 시리즈, FSv2 시리즈, M 시리즈, GS 시리즈, NCv2 시리즈, NCv3 시리즈 또는 Ls 시리즈 VM 중 하나에서 새 VM을 배포하는 것이 좋습니다. 위의 권장 크기를 사용하지 않는 기존 VM 또는 VM에 큰 디스크를 연결하면 성능이 저하될 수 있습니다.

**GA에서 더 높은 IOPS & 대역폭을 얻으려면 더 큰 디스크 크기 미리 보기 중에 배포된 디스크(>4 TiB)를 업그레이드하려면 어떻게 해야 합니까?**

디스크가 연결된 VM을 중지하고 시작하거나 디스크를 분리하여 다시 연결할 수 있습니다. GA의 프리미엄 SSD와 표준 SSD 모두에서 더 큰 디스크 크기의 성능 목표가 증가했습니다.

**8TiB, 16 TiB 및 32 TiB의 관리디스크 크기는 어떤 영역입니까?**

8 TiB, 16 TiB 및 32 TiB 디스크 SCO는 글로벌 Azure, Microsoft Azure 정부 및 Azure China 21Vianet의 모든 지역에서 지원됩니다.

**모든 디스크 크기에서 호스트 캐싱을 사용하도록 지원합니까?**

ReadOnly의 호스트 캐칭을 지원하며 4TiB 미만의 디스크 크기에 대해 읽기/쓰기를 지원합니다. 4TiB를 초과하는 디스크 크기의 경우 없음 이외의 설정 캐싱 옵션은 지원하지 않습니다. VM에 캐시된 데이터를 사용하여 성능 향상을 확인할 수 있을 수 있는 더 작은 디스크 크기에 대한 캐싱을 활용하는 것이 좋습니다.

## <a name="what-if-my-question-isnt-answered-here"></a>여기서 내 질문에 대답하지 않으면 어떻게 하나요?

질문하려는 내용이 아래 목록에 나와 있지 않으면 알려 주세요. 대답을 확인하는 방법을 알려 드리겠습니다. 설명에 있는 이 문서의 끝에 질문을 게시할 수 있습니다. Azure Storage 팀 및 다른 커뮤니티 구성원과 이 문서에 대한 의견을 교환하려면 MSDN [Azure Storage 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)을 사용합니다.

기능을 요청하려면 요청 내용과 아이디어를 [Azure Storage 피드백 포럼](https://feedback.azure.com/forums/217298-storage)으로 제출해 주세요.
