---
title: "Blob에 대한 Azure 핫, 쿨 및 보관 저장소 | Microsoft Docs"
description: "Azure Blob Storage 계정에 대한 핫, 쿨 및 보관 저장소입니다."
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 544b11d74a926fe62b8ceca51570ce9d2ee7e6e7
ms.contentlocale: ko-kr
ms.lasthandoff: 08/25/2017

---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Azure Blob Storage: 핫, 쿨 및 보관(미리 보기) 저장소 계층

## <a name="overview"></a>개요

Azure Storage는 Blob 개체 저장소에 세 가지 저장소 계층을 제공하므로 사용하는 방식에 따라 가장 비용 효율적으로 데이터를 저장할 수 있습니다. Azure **핫 저장소 계층** 은 자주 액세스하는 데이터 저장에 최적화되어 있습니다. Azure **쿨 저장소 계층**은 드물게 액세스되고 최소 한 달 동안 저장되는 데이터를 저장하는 데 최적화되어 있습니다. [보관 저장소 계층(미리 보기)](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering)은 거의 액세스되지 않고 유연한 대기 시간을 요구(시간 기준)하는 최소 6달 동안 저장되는 데이터를 저장하는 데 최적화되어 있습니다. *보관* 저장소 계층은 전체 저장소 계정이 아니라 Blob 수준에서만 사용할 수 있습니다. 쿨 저장소 계층의 데이터에는 약간 낮은 가용성을 허용할 수 있지만 높은 내구성과 핫 데이터와 비슷한 액세스 시간 및 처리량 특성이 필요합니다. 쿨 데이터 및 보관 데이터의 경우 저장소 비용을 크게 낮추기 위해 가용성 SLA는 약간 낮고 액세스 비용은 다소 높아도 됩니다.

오늘날 클라우드에 저장된 데이터는 기하급수적으로 증가하고 있습니다. 저장소 확장 요구에 대한 비용을 관리하려면 액세스 빈도 및 계획 보존 기간과 같은 속성을 기반으로 하여 데이터를 구성하는 것이 좋습니다. 클라우드에 저장되는 데이터는 수명 기간 동안 생성, 처리 및 액세스되는 방식이 서로 다를 수 있습니다. 일부 데이터는 수명 기간 전반에 걸쳐 활발하게 액세스되고 수정됩니다. 일부 데이터는 수명 기간 초반에는 빈번하게 액세스되지만 데이터가 오래될수록 액세스 빈도가 급격하게 떨어집니다. 일부 데이터는 클라우드에 유휴 상태로 유지되며 드물지만 한 번 액세스됩니다.

이러한 각각의 데이터 액세스 시나리오에서는 특정 액세스 패턴에 맞게 최적화되어 차별화된 저장소 계층이 유익합니다. Azure Blob Storage는 핫, 쿨 및 보관 저장소 계층에서 별도의 가격 책정 모델을 통한 차별화된 저장소 계층에 대한 이러한 수요를 해결합니다.

## <a name="blob-storage-accounts"></a>Blob 저장소 계정

**Blob 저장소 계정**은 Azure Storage에서 Blob(개체)과 같은 구조화되지 않은 데이터를 저장하기 위한 특수 저장소 계정입니다. 이제 액세스 패턴에 따라 Blob Storage 계정을 사용하여 계정 수준에서 핫 및 쿨 저장소 계정 중에 하나를 선택하고 Blob 수준에서 핫, 쿨 및 보관 계층 중에 하나를 선택할 수 있습니다. 액세스된 쿨 데이터를 가장 낮은 저장소 비용으로 저장하고, 자주 사용하지 않는 액세스된 쿨 데이터를 핫보다 낮은 저장소 비용으로 저장하고, 자주 사용하는 액세스된 핫 데이터를 가장 낮은 액세스 비용으로 저장합니다. Blob Storage 계정은 기존 범용 저장소 계정과 유사합니다. 블록 Blob과 연결 Blob에 대한 100% API 일관성을 포함하여 현재 사용하는 뛰어난 내구성, 가용성, 확장성 및 성능 기능을 모두 공유합니다.

> [!NOTE]
> Blob 저장소 계정은 블록 및 추가 Blob만 지원하고 페이지 Blob은 지원하지 않습니다.

Blob 저장소 계정은 **액세스 계층** 속성을 표시하며, 이 속성을 사용하면 계정에 저장된 데이터에 따라 저장소 계층을 **핫** 또는 **쿨**로 지정할 수 있습니다. 데이터의 사용 패턴에 변화가 있으면 언제든 이 저장소 계층 간을 전환할 수 있습니다. 보관 계층(미리 보기)은 Blob 수준에서만 적용할 수 있습니다.

> [!NOTE]
> 저장소 계층을 변경하면 추가 요금이 발생할 수 있습니다. 자세한 내용은 [가격 책정 및 대금 청구](#pricing-and-billing)를 참조하세요.

### <a name="hot-access-tier"></a>핫 액세스 계층

핫 저장소 계층에 대한 사용 예시 시나리오는 다음과 같습니다.

* 현재 사용 중이거나 자주 액세스할 것으로 예상되는 데이터(읽기 및 쓰기)
* 처리 및 쿨 저장소 계층으로의 마이그레이션이 준비된 데이터

### <a name="cool-access-tier"></a>쿨 액세스 계층

쿨 저장소 계층에 대한 사용 예시 시나리오는 다음과 같습니다.

* 단기 백업 및 재해 복구 데이터 집합
* 자주 감상하지 않으나, 액세스할 때 즉시 사용할 수 있어야 하는 오래된 미디어 콘텐츠
* 향후 처리를 위해 더 많은 데이터를 수집하는 동안 경제적으로 저장되어야 하는 대용량 데이터 집합 (*예:* 과학적 데이터의 장기 저장, 제조 설비의 원시 원격 분석 데이터)

### <a name="archive-access-tier-preview"></a>보관 액세스 계층(미리 보기)

[보관 저장소](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering)는 저장소 비용아 가장 낮고 핫 및 쿨 저장소에 비해 데이터 검색 비용이 높습니다.

Blob이 보관 저장소에 있지만 읽거나, 복사하거나, 덮어쓰거나, 수정할 수 없습니다. 또는 보관 저장소에서 Blob의 스냅숏을 만들 수도 없습니다. 그러나 기존 작업을 사용하여 Blob 속성/메타데이터를 삭제하고 나열하고 가져오거나 Blob의 계층을 변경할 수 있습니다. 보관 저장소에서 데이터를 읽으려면 먼저 핫 또는 쿨로 Blob의 계층을 변경해야 합니다. 이 프로세스는 리하이드레이션이라고 하고 50GB 미만인 Blob를 완료하는 데 최대 15시간이 걸릴 수 있습니다. 그 이상의 Blob에 필요한 추가 시간은 Blob 처리량 제한에 따라 다릅니다.

리하이드레이션 중에 계층이 변경되었는지 여부를 확인하려면 "보관 상태" Blob 속성을 확인할 수 있습니다. 상태는 대상 계층에 따라 "rehydrate-pending-to-hot" 또는 "rehydrate-pending-to-cool"을 읽습니다. 완료되면 "보관 상태" Blob 속성이 제거되고 "액세스 계층" Blob 속성은 핫 또는 쿨 계층을 반영합니다.  

보관 저장소 계층에 대한 사용 예시 시나리오는 다음과 같습니다.

* 장기 백업, 보관 및 재해 복구 데이터 집합
* 사용 가능한 최종 형태로 처리된 후에도 보존할 필요가 있는 원래(원시) 데이터. (*예:* 다른 형식으로 코드 변환한 원시 미디어 파일)
* 장기간 저장할 필요가 있거나 거의 액세스하지 않는 규정 준수 및 보관 데이터. (*예:* 보안 카메라 영상, 의료 기관의 오래된 X레이/MRI, 금융 기관의 고객 통화에 대한 오디오 녹음 및 내용 자료)

### <a name="recommendations"></a>권장 사항

저장소 계정에 대한 자세한 내용은 [Azure 저장소 계정 정보](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 를 참조하세요.

블록 또는 연결 Blob 저장소만 필요한 응용 프로그램의 경우 Blob 저장소 계정을 사용하여 차별화된 계층형 저장소 가격 모델을 활용하는 것이 좋습니다. 그러나 다음과 같이 범용 저장소 계정을 사용할 수 있는 특정 상황에서는 이것이 가능하지 않을 수 있습니다.

* 테이블, 쿼리 또는 파일을 사용하고 동일한 저장소 계정에 Blob를 저장하려는 경우. 동일한 공유 키를 갖는 경우를 제외하고 동일한 계정에 이 모두를 저장하는 데는 기술적인 이점이 없습니다.

* 여전히 클래식 배포 모델을 사용해야 합니다. Blob 저장소 계정은 Azure 리소스 관리자 배포 모델을 통해서만 사용할 수 있습니다.

* 페이지 Blob을 사용해야 합니다. Blob 저장소 계정은 페이지 Blob을 지원하지 않습니다. 일반적으로 페이지 Blob가 특별히 필요한 상황이 아니라면 블록 Blob를 사용하는 것이 좋습니다.

* 2014-02-14 이전 버전인 [저장소 서비스 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 나, 4.x 미만인 클라이언트 라이브러리를 사용하며 응용 프로그램을 업그레이드할 수 없습니다.

> [!NOTE]
> Blob 저장소 계정은 현재 모든 Azure 지역에서 지원됩니다.
 

## <a name="blob-level-tiering-feature-preview"></a>Blob 수준 계층 기능(미리 보기)

Blob 수준 계층을 사용하면 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)이라는 단일 작업을 사용하여 개체 수준에서 데이터의 계층을 변경할 수 있습니다. 사용 패턴이 변경되면 계정 간에 데이터를 이동하지 않고 핫, 쿨 또는 보관 계층 간에 Blob의 액세스 계층을 쉽게 변경할 수 있습니다. 모든 계층 변경 내용은 Blob가 보관에서 리하이드레이션하는 경우를 제외하고 즉시 적용됩니다. 세 저장소 계층에 있는 Blob는 동일한 계정 내에서 공존할 수 있습니다. 명시적으로 할당된 계층이 없는 모든 Blob는 계정 액세스 계층 설정에서 계층을 상속합니다.

미리 보기 상태에서 이러한 기능을 사용하려면 [Azure 보관 및 Blob 수준 계층화 블로그 알림](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering)의 지침에 따릅니다.

Blob 수준 계층 구성에 대한 미리 보기 기간 동안 적용되는 몇 가지 제한 사항은 다음에 나열됩니다.

* 미리 보기 등록 지원 보관 저장소가 성공한 후에 미국 동부 2에서 만든 새 Blob 저장소 계정입니다.

* 미리 보기 등록 지원 Blob 수준 계층이 성공한 후에 공용 지역에서 만든 새 Blob 저장소 계정입니다.

* Blob 수준 계층 및 보관 저장소는 [LRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage) 저장소에서만 지원됩니다. [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) 및 [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage)는 나중에 지원될 예정입니다.

* 스냅숏으로 Blob의 계층을 변경할 수 없습니다.

* 보관 저장소에서 Blob을 복사하거나 스냅숏을 만들지 않을 수 있습니다.

## <a name="comparison-of-the-storage-tiers"></a>저장소 계층 비교

다음 표에서는 핫 및 쿨 저장소 계층의 비교를 보여줍니다. 보관 Blob 수준 계층은 미리 보기 상태이므로 이에 대한 SLA가 없습니다.

| | **핫 저장소 계층** | **쿨 저장소 계층** |
| ---- | ----- | ----- |
| **가용성** | 99.9% | 99% |
| **가용성** <br> **(RA-GRS 읽기)**| 99.99% | 99.9% |
| **사용 요금** | 저장소 비용 더 높음, 액세스 및 트랜잭션 비용 더 낮음 | 저장소 비용 더 낮음, 액세스 및 트랜잭션 비용 더 높음 |
| **최소 개체 크기** | 해당 없음 | 해당 없음 |
| **최소 저장 기간** | 해당 없음 | 해당 없음 |
| **대기 시간** <br> **(첫 번째 바이트까지의 시간)** | 밀리초 | 밀리초 |
| **확장성 및 성능 대상** | 범용 저장소 계정과 동일 | 범용 저장소 계정과 동일 |

> [!NOTE]
> Blob 저장소 계정은 범용 저장소 계정과 동일한 성능 및 확장성 목표를 지원합니다. 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (영문)를 참조하십시오.


## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구
Blob Storage 계정에서는 저장소 계층에 따라 Blob Storage의 가격 책정 모델을 사용합니다. Blob 저장소 계정을 사용하는 경우 다음과 같은 청구 고려 사항이 적용됩니다.

* **저장소 비용**: 저장된 데이터 크기 외에도, 데이터 저장 비용은 저장소 계층에 따라 달라집니다. 기가바이트당 비용은 핫 저장소 계층보다 쿨 저장소 계층이 더 저렴합니다.

* **데이터 액세스 비용**: 쿨 저장소 계층의 데이터의 경우 읽기 및 쓰기에 대해 기가바이트당 데이터 액세스 요금이 청구됩니다.

* **트랜잭션 비용**: 두 계층에 모두 트랜잭션당 요금이 있습니다. 그러나 쿨 저장소 계층의 트랜잭션당 비용이 핫 저장소 계층보다 높습니다.

* **지역에서 복제 데이터 전송 비용**: GRS 및 RA-GRS를 포함하여 지역에서 복제가 구성된 계정에만 해당합니다. 지역 복제 데이터 전송에는 기가바이트당 요금이 발생합니다.

* **아웃바운드 데이터 전송 비용**: 아웃바운드 데이터 전송(Azure 지역 밖으로 전송된 데이터)에서는 기가바이트당 요금을 기준으로 대역폭 사용 요금이 발생하며 범용 저장소 계정과 같습니다.

* **저장소 계층 변경**: 저장소 계층을 쿨에서 핫으로 변경하면 트랜잭션마다 저장소 계정에서 모든 기존 데이터를 읽는 것과 같은 요금이 발생합니다. 반대로 저장소 계층을 핫에서 쿨로 변경하는 것은 무료입니다.

> [!NOTE]
> Blob Storage 계정의 가격 책정 모델에 대한 자세한 내용은 [Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지를 참조하세요. 아웃바운드 데이터 전송 요금에 대한 자세한 내용은 [데이터 전송 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 페이지를 참조하세요.

## <a name="quickstart"></a>빠른 시작

이 섹션에서는 Azure Portal을 사용하여 다음 시나리오를 시연하겠습니다.

* 방법: Blob 저장소 계정 만들기
* 방법: Blob 저장소 계정 관리

이 설정이 전체 저장소 계정에 적용되므로 다음 예제에서 보관할 액세스 계층을 설정할 수 없습니다. 보관은 특정 Blob에만 설정할 수 있습니다.

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Azure 포털을 사용하여 Blob 저장소 계정 만들기

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **새로 만들기** > **데이터 + 저장소** > **저장소 계정**을 차례로 선택합니다.

3. 저장소 계정의 이름을 입력합니다.
   
    이 이름은 전역적으로 고유해야 합니다. 저장소 계정의 개체에 액세스하는 데 사용되는 URL의 일부로 사용됩니다.  

4. **Resource Manager** 를 배포 모델로 선택합니다.
   
    계층화된 저장소는 Resource Manager 저장소 계정과 함께 사용할 수 있습니다. 새 리소스에 권장되는 배포 모델입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.  

5. 계정 종류 드롭다운 목록에서 **Blob 저장소**를 선택합니다.
   
    저장소 계정 유형을 선택하는 위치입니다. 범용 저장소에서 계층화된 저장소를 사용할 수 없습니다. Blob 저장소 유형 계정에서만 사용할 수 있습니다.     
   
    이를 선택하면 성능 계층은 표준으로 설정됩니다. 계층화된 저장소는 프리미엄 성능 계층과 함께 사용할 수 없습니다.

6. 저장소 계정의 복제 옵션을 **LRS**, **GRS** 또는 **RA-GRS**로 선택합니다. 기본값은 **RA-GRS**입니다.
   
    LRS = 로컬 중복 저장소; GRS = 지역 중복 저장소(2개 지역); RA-GRS는 읽기 액세스 지역 중복 저장소입니다(두 번째 저장소에 대한 읽기 액세스 권한이 있는 2개 지역).
   
    Azure 저장소 복제 옵션에 대한 자세한 내용은 아래의 [Azure 저장소 복제](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 확인하세요.

7. 요구 사항에 적합한 저장소 계층을 선택합니다. 즉 **액세스 계층**을 **쿨** 또는 **핫**으로 설정합니다. 기본값은 **핫**입니다. 

8. 새 저장소 계정을 만들려는 구독을 선택합니다.

9. 새 리소스 그룹을 지정하거나 기존 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

10. 저장소 계정에 대한 지역을 선택합니다.

11. **만들기** 를 클릭하여 저장소 계정을 만들 수 있습니다.

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Azure 포털을 사용하여 Blob 저장소 계정의 저장소 계층 변경

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. 저장소 계정으로 이동하려면 모든 리소스를 선택하고 저장소 계정을 선택합니다.

3. 설정 블레이드에서 **구성** 을 클릭하여 계정 구성을 보기 및/또는 변경합니다.

4. 요구 사항에 적합한 저장소 계층을 선택합니다. 즉 **액세스 계층**을 **쿨** 또는 **핫**으로 설정합니다.

5. 블레이드 위쪽에서 저장을 클릭합니다.

> [!NOTE]
> 저장소 계층을 변경하면 추가 요금이 발생할 수 있습니다. 자세한 내용은 [가격 책정 및 대금 청구](#pricing-and-billing)를 참조하세요.


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Blob 저장소 계정 평가 및 Blob 저장소 계정으로 마이그레이션
이 섹션은 Blob 저장소 계정 사용으로의 원활한 마이그레이션을 지원하기 위한 것입니다. 두 가지 사용자 시나리오가 있습니다.

* 기존 범용 저장소 계정이 있고 올바른 저장소 계층을 사용하여 Blob 저장소 계정에 대한 변경을 평가하려고 합니다.
* Blob 저장소 계정을 사용하도록 결정했거나 이미 저장소 계정이 하나 있고 핫 또는 쿨 저장소 계층을 사용해야 하는지 여부를 평가하려고 합니다.

두 경우 모두 비즈니스의 첫 번째 순서는 Blob 저장소 계정에 저장된 데이터를 저장 및 액세스하는 비용을 예측하고 현재 비용과 비교하는 것입니다.

## <a name="evaluating-blob-storage-account-tiers"></a>Blob 저장소 계정 계층 평가

Blob Storage 계정에 저장된 데이터를 저장 및 액세스하는 비용을 예상하기 위해 기존 사용 패턴을 평가하거나 예상된 사용 패턴을 계산해야 합니다. 일반적으로 다음을 파악해야 합니다.

* 저장소 사용량 – 데이터 저장 규모 및 월 기준 변화

* 저장소 액세스 패턴 - 계정에서 읽고 쓰는 데이터의 크기(새 데이터 포함) 데이터 액세스에 사용되는 트랜잭션 양 및 트랜잭션 유형

## <a name="monitoring-existing-storage-accounts"></a>기존 저장소 계정 모니터링

기존 저장소 계정을 모니터링하고 이 데이터를 수집하기 위해 로깅을 수행하고 저장소 계정에 대한 메트릭 데이터를 제공하는 Azure 저장소 분석을 사용할 수 있습니다. 저장소 분석으로 범용 저장소 계정 및 Blob 저장소 계정을 위한 Blob 저장소 서비스에 대한 요청에 대한 집계된 트랜잭션 통계 및 용량 데이터를 포함하는 메트릭을 저장할 수 있습니다. 이 데이터는 동일한 저장소 계정에서 잘 알려진 테이블에 저장됩니다.

자세한 내용은 [저장소 분석 메트릭 정보](https://msdn.microsoft.com/library/azure/hh343258.aspx) 및 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/azure/hh343264.aspx)를 참조하세요.

> [!NOTE]
> Blob 저장소 계정은 해당 계정에 대한 메트릭 데이터 저장 및 액세스에 대해서만 테이블 서비스 끝점을 노출합니다.

Blob Storage 서비스에 대한 저장소 사용량을 모니터링하려면 용량 메트릭을 활성화해야 합니다.
이를 활성화하면 용량 데이터는 저장소 계정의 Blob service에 대해 매일 기록되고 동일한 저장소 계정 내에서 *$MetricsCapacityBlob* 테이블에 작성된 테이블 항목으로 기록됩니다.

Blob Storage 서비스에 대한 데이터 액세스 패턴을 모니터링하려면 API 수준에서 시간당 트랜잭션 메트릭을 활성화해야 합니다. 이를 활성화하면 API당 트랜잭션은 매시간 집계되며 동일한 저장소 계정 내에서 *$MetricsHourPrimaryTransactionsBlob* 테이블로 작성된 테이블 항목으로 기록됩니다. *$MetricsHourSecondaryTransactionsBlob* 테이블은 RA-GRS 저장소 계정을 사용하는 경우 보조 끝점에 트랜잭션을 기록합니다.

> [!NOTE]
> 페이지 Blob과 블록 및 추가 Blob 데이터와 함께 가상 컴퓨터 디스크를 저장한 범용 저장소 계정이 있는 경우 이 예측 프로세스는 적용되지 않습니다. 블록에 대한 Blob의 유형 및 Blob Storage 계정으로 마이그레이션될 수 있는 추가 Blob에 따라 용량 및 트랜잭션 메트릭을 구별하는 방법이 없기 때문입니다.

데이터 소비 및 액세스 패턴을 대략적으로 파악하려면 정기적 사용을 나타내는 메트릭의 보존 기간을 선택하고 추정하는 것이 좋습니다. 하나의 옵션은 7일 동안 메트릭 데이터를 보유하고 월말에 분석을 위해 매주 데이터를 수집하는 것입니다. 또 다른 옵션은 지난 30일 동안의 메트릭 데이터를 보존하고 30일 기간이 끝날 때 데이터를 수집하고 분석하는 것입니다.

메트릭 데이터 사용, 수집 및 보기는 [Azure Storage 메트릭 사용 및 메트릭 데이터 보기](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

> [!NOTE]
> 분석 데이터 저장, 액세스 및 다운로드는 일반 사용자 데이터와 마찬가지로 청구됩니다.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>사용 현황 메트릭을 활용하여 비용 추정

### <a name="storage-costs"></a>저장소 비용

*'data'* 행 키가 있는 *$MetricsCapacityBlob* 용량 메트릭 테이블의 최신 항목은 사용자 데이터에서 사용하는 저장소 용량을 보여 줍니다. *'analytics'* 행 키가 있는 *$MetricsCapacityBlob* 용량 메트릭 테이블의 최신 항목은 분석 로그에서 사용하는 저장소 용량을 보여 줍니다.

사용자 데이터 및 분석 로그(활성화된 경우)에서 소비되는 이 전체 용량은 저장소 계정에서 데이터 저장의 비용을 예측하는 데 사용할 수 있습니다. 범용 저장소 계정에서 블록 및 추가 Blob에 대한 저장소 비용을 예상하는 데 동일한 메서드를 사용할 수도 있습니다.

### <a name="transaction-costs"></a>트랜잭션 비용

트랜잭션 메트릭 테이블에서 API에 대한 모든 항목에 대한 *'TotalBillableRequests'*의 합계는 특정 API에 대한 트랜잭션의 총 수를 나타냅니다. *예를 들어* 지정된 기간의 총 *'GetBlob'* 트랜잭션 수는 *'user;GetBlob'* 행 키가 있는 모든 항목에 대해 청구 가능한 요청의 총합으로 계산될 수 있습니다.

트랜잭션은 서로 다른 가격이 책정되므로 Blob Storage 계정에 대한 트랜잭션 비용을 예상하려면 트랜잭션을 3개의 그룹으로 세분화해야 합니다.

* *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* 및 *'CopyBlob'*과 같은 쓰기 트랜잭션
* *'DeleteBlob'* 및 *'DeleteContainer'*와 같은 삭제 트랜잭션
* 모든 다른 트랜잭션.

범용 저장소 계정에 대한 트랜잭션 비용을 예상하려면 작업/API에 관계 없이 모든 트랜잭션을 집계해야 합니다.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>데이터 액세스 및 지역에서 복제 데이터 전송 비용

저장소 분석은 저장소 계정에서 읽고 쓰는 데이터의 양을 제공하지 않지만 트랜잭션 메트릭 테이블을 확인하여 대략적으로 예상할 수 있습니다. 트랜잭션 메트릭 테이블에서 API에 대한 모든 항목에 대한 *'TotalIngress'* 의 합계는 특정 API에 대한 수신 데이터의 총 크기를 바이트로 나타냅니다. 마찬가지로 *'TotalEgress'* 의 합계는 송신 데이터의 총 크기를 바이트로 나타냅니다.

Blob Storage 계정에 대한 데이터 액세스 비용을 예상하려면 트랜잭션을 2개의 그룹으로 세분화해야 합니다. 

* 저장소 계정에서 검색되는 데이터 크기는 주로 *'GetBlob'* 및 *'CopyBlob'* 작업에 대한 *'TotalEgress'* 합계를 확인하여 예상할 수 있습니다.

* 저장소 계정에 작성되는 데이터 크기는 주로 *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* 및 *'AppendBlock'* 작업에 대한 *'TotalIngress'* 합계를 확인하여 예상할 수 있습니다.

Blob Storage 계정에 대한 지역에서 복제 데이터 전송의 비용은 GRS 또는 RA-GRS 저장소 계정을 사용하는 경우 작성된 데이터의 양에 대한 추정을 사용하여 계산할 수도 있습니다.

> [!NOTE]
> 핫 또는 쿨 저장소 계층을 사용하는 비용 계산에 대한 자세한 예제는 *Azure Storage 가격 책정 페이지* 에서 [핫 및 쿨 액세스 계층이란 무엇이며 무슨 계층을 사용할지 어떻게 결정하나요?](https://azure.microsoft.com/pricing/details/storage/)에 로그인합니다.
 
## <a name="migrating-existing-data"></a>기존 데이터 마이그레이션

Blob 저장소 계정은 저장 전용 블록 및 추가 Blob에 맞게 특별히 설정됩니다. 테이블, 큐, 파일 및 디스크는 물론 Blob도 저장할 수 있는 기존의 범용 저장소 계정은 Blob Storage 계정으로 변환할 수 없습니다. 저장소 계층을 사용하려면, 새로운 Blob Storage 계정을 만들어서 기존 데이터를 새로 만든 계정으로 마이그레이션해야 합니다.

다음 방법을 통해 기존 데이터를 온-프레미스 저장소 장치, 타사 클라우드 저장소 공급자 또는 기존 Azure 범용 저장소 계정에서 Blob Storage 계정으로 마이그레이션할 수 있습니다.

### <a name="azcopy"></a>AzCopy

AzCopy는 Azure 저장소의 데이터를 고속으로 복사하기 위해 설계된 Windows 명령줄 유틸리티입니다. AzCopy를 사용하여 기존 범용 저장소 계정의 데이터를 Blob 저장소 계정으로 복사하거나, 온-프레미스 저장소 장치의 데이터를 Blob 저장소 계정에 업로드할 수 있습니다.

자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

### <a name="data-movement-library"></a>데이터 이동 라이브러리

.NET용 Azure 저장소 데이터 이동 라이브러리는 AzCopy를 구동하는 핵심 데이터 이동 프레임워크를 기반으로 합니다. 이 라이브러리는 AzCopy와 유사하게 성능이 높고 안정적이며 사용이 간편한 데이터 전송 작업을 제공합니다. 이 라이브러리를 사용하면 AzCopy의 외부 인스턴스를 실행 및 모니터링하지 않고도 응용 프로그램에서 기본적으로 AzCopy가 제공하는 기능을 완벽히 활용할 수 있습니다.

자세한 내용은 [.NET용 Azure 저장소 데이터 이동 라이브러리](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>REST API 또는 클라이언트 라이브러리

Azure 클라이언트 라이브러리 또는 Azure 저장소 서비스 REST API 중 하나를 통해 데이터를 Blob 저장소 계정으로 마이그레이션하는 사용자 지정 응용 프로그램을 만들 수 있습니다. Azure 저장소는 NET, Java, C++, Node.JS, PHP, Ruby, Python 등, 여러 언어와 플랫폼을 위한 다양한 클라이언트 라이브러리를 제공합니다. 이 클라이언트 라이브러리는 재시도 논리, 로깅, 병렬 업로드와 같은 고급 기능을 제공합니다. HTTP/HTTPS 요청이 가능한 모든 언어로 호출할 수 있는 REST API에 대해 바로 개발할 수도 있습니다.

자세한 내용은 [Azure Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md)을 참조하세요.

> [!NOTE]
> 클라이언트 쪽 암호화를 사용하여 암호화된 BLOB은 BLOB과 함께 저장되는 암호화 관련 메타데이터를 저장합니다. 이것은 모든 복사 메커니즘이 Blob 메타데이터 특히, 암호화 관련 메타데이터가 보존되는지 확인해야 하기 때문에 매우 중요합니다. Blob을 이러한 메타데이터 없이 복사하면 Blob 콘텐츠를 다시 검색할 수 없습니다. 암호화 관련 메타데이터에 대한 자세한 내용은 [Azure Storage 클라이언트 쪽 암호화](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
 
## <a name="faq"></a>FAQ

1. **기존 저장소 계정을 계속 사용할 수 있나요?**
   
    예, 기존 저장소 계정은 계속 사용할 수 있으며 가격 책정이나 기능은 바뀌지 않습니다.  저장소 계층을 선택할 수 있는 기능이 없으며, 향후에도 계층 지정 기능을 사용할 수 없을 것입니다.

2. **Blob 저장소 계정을 왜 사용해야 하며 언제 사용을 시작해야 하나요?**
   
    Blob 저장소 계정은 Blob 저장에 특화된 계정이며 Blob 중심적인 새 기능을 제공 받을 수 있습니다. 앞으로 계층형 저장소 및 계층 지정 등과 같은 기능이 이 계정 유형에 맞춰 도입될 것이므로 Blob 저장에는 Blob 저장소 계정을 권장합니다. 그러나 비즈니스 요구 사항에 맞게 마이그레이션 시점을 결정하는 것은 사용자의 몫입니다.

3. **기존 저장소 계정을 Blob 저장소 계정으로 변환할 수 있나요?**
   
    안 됩니다. Blob Storage 계정은 다른 종류의 저장소 계정이며, 새로 계정을 만들고 이전에 설명한 대로 데이터를 마이그레이션해야 합니다.

4. **동일한 계정에서 두 저장소 계층에 모두 데이터를 저장할 수 있나요?**
   
    계정 수준에서 설정된 *’액세스 계층’* 특성은 저장소 계층 값을 나타내며 해당 계정의 모든 개체에 적용됩니다. 그러나 Blob 수준 계층(미리 보기) 기능을 사용하면 특정 Blob에 대한 액세스 계층을 설정할 수 있습니다. 또한 계정에 대한 액세스 계층 설정을 덮어씁니다. 

5. **Blob 저장소 계정의 저장소 계층을 변경할 수 있나요?**
   
    예. 저장소 계정에 *’액세스 계층'* 특성을 설정하여 저장소 계층을 변경할 수 있습니다. 저장소 계층을 변경하면 계정에 저장된 모든 개체에 적용됩니다. 저장소 계층을 핫에서 쿨로 변경하면 요금이 발생하지 않지만, 쿨에서 핫으로 변경하면 계정의 모든 데이터 읽기에 대해 GB 기준 요금이 발생합니다.

6. **Blob 저장소 계정의 저장소 계층을 얼마나 자주 변경할 수 있나요?**
   
    저장소 계층을 변경하는 빈도에 대해서는 제약을 적용하지 않고 있으나 저장소 계층을 쿨에서 핫으로 변경하면 상당한 요금이 발생할 수 있습니다. 자주 저장소 계층을 변경하는 것은 좋지 않습니다.

7. **쿨 저장소 계층의 Blob이 핫 저장소 계층의 Blob과 다르게 작동하나요?**
   
    핫 저장소 계층의 Blob에는 범용 저장소 계정의 Blob과 같은 대기 시간이 있습니다. 쿨 저장소 계층의 Blob에는 범용 저장소 계정의 Blob과 유사한(밀리초 단위) 대기 시간이 있습니다.
   
    쿨 저장소 계층의 Blob은 핫 저장소 계층의 Blob보다 가용성 서비스 수준(SLA)이 약간 낮습니다. 자세한 내용은 [저장소용 SLA](https://azure.microsoft.com/support/legal/sla/storage)를 참조하세요.

8. **페이지 Blob과 가상 컴퓨터 디스크를 Blob 저장소 계정에 저장할 수 있나요?**
   
    Blob 저장소 계정은 블록 및 추가 Blob만 지원하고 페이지 Blob은 지원하지 않습니다. Azure 가상 컴퓨터 디스크는 페이지 Blob에 의해 지원되며, 결과적으로 Blob 저장소 계정은 가상 컴퓨터 디스크를 저장하는 데 사용될 수 없습니다. 하지만 가상 컴퓨터 디스크의 백업을 Blob 저장소 계정의 블록 Blob으로 저장하는 것은 가능합니다.

9. **Blob Storage 계정을 사용하려면 기존 응용 프로그램을 변경해야 하나요?**
   
    Blob 저장소 계정은 블록 및 추가 Blob에 대한 범용 저장소 계정과 API가 100% 동일합니다. 응용 프로그램에서 블록 Blob 또는 추가 Blob을 사용하고 [저장소 서비스 REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) 2014-02-14 버전 이상을 사용하는 한 응용 프로그램은 작동합니다. 이전 프로토콜 버전을 사용할 경우 응용 프로그램을 업데이트하여 새 버전을 사용해야 두 저장소 계정에서 모두 원활하게 작업할 수 있습니다. 일반적으로 사용하는 저장소 계정 유형에 관계없이 항상 최신 버전을 권장합니다.

10. **사용자 환경이 변경되나요?**
    
    Blob 저장소 계정은 블록 및 추가 Blob 저장에 대해 범용 저장소 계정과 매우 유사하며, 높은 내구성 및 가용성, 확장성, 성능 및 보안을 비롯한 Azure 저장소의 모든 주요 기능을 지원합니다. Blob 저장소 계정 고유의 특징 및 제한 사항과 위에서 설명한 저장소 계층을 제외한 나머지는 모두 같습니다.

## <a name="next-steps"></a>다음 단계

### <a name="evaluate-blob-storage-accounts"></a>Blob 저장소 계정 평가

[지역별 Blob 저장소 계정의 가용성 확인](https://azure.microsoft.com/regions/#services)

[Azure 저장소 메트릭을 활성화하여 현재 저장소 계정의 사용 현황 평가](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[지역별 Blob 저장소 가격 확인](https://azure.microsoft.com/pricing/details/storage/)

[데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Blob 저장소 계정 사용 시작

[Azure Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md)

[Azure 저장소의 데이터 이동](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[AzCopy 명령줄 유틸리티로 데이터 전송](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[저장소 계정 찾아보기 및 탐색](http://storageexplorer.com/)
