---
title: 컨테이너 이미지 스토리지
description: 컨테이너 이미지 및 기타 아티팩트가 보안, 중복성 및 용량을 포함 하 여 Azure Container Registry에 저장 되는 방식에 대 한 세부 정보입니다.
ms.topic: article
ms.date: 03/03/2021
ms.custom: references_regions
ms.openlocfilehash: ec4328b44d5493b8d765fa30c548adc3d747d446
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183270"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry의 컨테이너 이미지 스토리지

모든 [기본, 표준 및 프리미엄](container-registry-skus.md) azure container registry는 미사용 암호화를 포함 하 여 고급 azure storage 기능을 활용 합니다. 다음 섹션에서는 ACR (Azure Container Registry)의 이미지 저장소 기능 및 제한 사항에 대해 설명 합니다.

## <a name="encryption-at-rest"></a>미사용 시 암호화

레지스트리의 모든 컨테이너 이미지와 기타 아티팩트는 미사용 시 암호화 됩니다. Azure는 저장하기 전에 이미지를 자동으로 암호화하고, 애플리케이션 및 서비스가 이미지를 끌어올 때 즉석에서 이미지의 암호를 해독합니다. 필요에 따라 [고객이 관리](container-registry-customer-managed-keys.md)하는 키를 사용 하 여 추가 암호화 계층을 적용 합니다.

## <a name="regional-storage"></a>지역 저장소

Azure Container Registry는 고객이 데이터 상주 및 규정 준수 요구 사항을 충족 하는 데 도움이 되도록 레지스트리가 생성 된 지역에 데이터를 저장 합니다.

데이터 센터 가동 중단 으로부터 보호 하기 위해 일부 지역은 [영역 중복성](zone-redundancy.md)을 제공 하며,이 경우 데이터는 특정 지역의 여러 데이터 센터에 걸쳐 복제 됩니다.

여러 지역에서의 성능을 향상 시키기 위해 데이터를 여러 지역에 저장 하려는 고객 또는 지역 가동 중단 시 복원 력을 유지 하려는 고객은 [지역에서 복제](container-registry-geo-replication.md)를 사용 하도록 설정 해야 합니다.

## <a name="geo-replication"></a>지역에서 복제

고가용성 보증이 필요한 시나리오의 경우 프리미엄 레지스트리의 [지역에서 복제](container-registry-geo-replication.md) 기능을 사용 하는 것이 좋습니다. 지역에서 복제는 지역 실패가 발생할 경우 레지스트리에 대 한 액세스를 잃지 않도록 보호 합니다. 지역에서 복제는 분산 개발 또는 배포 시나리오에서 빠른 푸시 및 풀을 위한 네트워크 근접 이미지 스토리지와 같은 다른 이점도 제공합니다.

## <a name="zone-redundancy"></a>영역 중복

복원 력 및 고가용성 Azure 컨테이너 레지스트리를 만들려면 선택적으로 Azure 지역 선택에서 [영역 중복성](zone-redundancy.md) 을 사용 하도록 설정 합니다. 프리미엄 서비스 계층의 기능으로, 영역 중복성은 Azure [가용성 영역](../availability-zones/az-overview.md) 을 사용 하 여 레지스트리를 사용 하도록 설정 된 각 지역에서 최소 3 개의 개별 영역에 복제 합니다. 지역 복제와 영역 중복성을 결합 하 여 레지스트리의 안정성과 성능을 향상 시킵니다. 

## <a name="scalable-storage"></a>확장 가능한 저장소

Azure Container Registry를 사용 하 여 필요한 만큼의 리포지토리, 이미지, 계층 또는 태그를 [레지스트리 저장소 제한](container-registry-skus.md#service-tier-features-and-limits)까지 만들 수 있습니다. 

많은 수의 리포지토리와 태그가 레지스트리의 성능에 영향을 줄 수 있습니다. 레지스트리 유지 관리 루틴의 일부로 사용 하지 않는 리포지토리, 태그 및 이미지를 주기적으로 삭제 하 고, 필요에 따라 태그가 없는 매니페스트에 대 한 [보존 정책을](container-registry-retention-policy.md) 설정 합니다. 삭제 한 후에는 리포지토리, 이미지 및 태그와 같은 삭제 된 레지스트리 리소스를 복구할 *수 없습니다* . 레지스트리 리소스를 삭제하는 방법에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조하세요.

## <a name="storage-cost"></a>스토리지 비용

가격 책정에 대한 자세한 내용은 [Azure Container Registry 가격 책정][pricing]을 참조하세요.

## <a name="next-steps"></a>다음 단계

기본, 표준, 프리미엄 컨테이너 레지스트리에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
