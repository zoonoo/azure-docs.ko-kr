---
title: 컨테이너 이미지 스토리지
description: 보안, 중복도 및 용량을 포함하여 컨테이너 이미지 및 기타 아티팩트가 Azure Container Registry에 저장되는 방식에 대한 세부 정보입니다.
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047748"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry의 컨테이너 이미지 스토리지

모든 [기본, 표준, 프리미엄](container-registry-skus.md) Azure Container Registry는 미사용 시 암호화 등 고급 Azure Storage 기능을 활용합니다. 다음 섹션에서는 ACR(Azure Container Registry)의 이미지 스토리지 기능과 제한을 둘 다 설명합니다.

## <a name="encryption-at-rest"></a>미사용 시 암호화

레지스트리의 모든 컨테이너 이미지 및 기타 아티팩트는 미사용 시 암호화됩니다. Azure는 저장하기 전에 이미지를 자동으로 암호화하고, 애플리케이션 및 서비스가 이미지를 끌어올 때 즉석에서 이미지의 암호를 해독합니다. 필요에 따라 [고객 관리형 키](container-registry-customer-managed-keys.md)를 사용하여 추가 암호화 계층을 적용합니다.

## <a name="regional-storage"></a>지역 스토리지

Azure Container Registry는 고객이 데이터 상주 및 규정 준수 요구 사항을 충족할 수 있도록 데이터를 레지스트리가 만들어진 지역에 저장합니다. 브라질 남부 및 동남 아시아를 제외한 모든 지역에서 Azure는 동일한 지리적 위치에서 쌍을 이루는 지역에 레지스트리 데이터를 저장할 수도 있습니다. 브라질 남부 및 동남 아시아 지역에서는 해당 지역의 데이터 상주 요구 사항을 충족하기 위해 레지스트리 데이터가 항상 해당 지역으로 한정됩니다.

지역 중단이 발생하는 경우 레지스트리 데이터는 사용할 수 없게 되고 자동으로 복구되지 않을 수 있습니다. 여러 지리적 위치에서의 성능 향상을 위해 레지스트리 데이터를 여러 지역에 저장하려는 고객이나 지역 중단이 발생할 때를 대비해 복원력을 갖추고자 하는 고객은 [지역 복제](container-registry-geo-replication.md)를 사용해야 합니다.

## <a name="geo-replication"></a>지역에서 복제

고가용성이 보장되어야 하는 시나리오의 경우 프리미엄 레지스트리의 [지역 복제](container-registry-geo-replication.md) 기능을 사용하는 것이 좋습니다. 지역 복제는 지역 오류가 발생할 경우 레지스트리에 대한 액세스 손실을 방지합니다. 지역에서 복제는 분산 개발 또는 배포 시나리오에서 빠른 푸시 및 풀을 위한 네트워크 근접 이미지 스토리지와 같은 다른 이점도 제공합니다.

## <a name="zone-redundancy"></a>영역 중복

복원력이 우수하고 가용성이 높은 Azure 컨테이너 레지스트리를 만들려면 선택한 Azure 지역에 필요에 따라 [영역 중복](zone-redundancy.md)을 사용합니다. 프리미엄 서비스 계층의 기능인 영역 중복은 Azure [가용성 영역](../availability-zones/az-overview.md)을 사용하여 레지스트리를 이 기능을 사용하는 지역에 속한 최소 3개의 개별 영역에 복제합니다. 영역 중복은 지역 복제와 결합하여 레지스트리의 안정성과 성능을 모두 향상합니다. 

## <a name="scalable-storage"></a>확장 가능한 저장소

Azure Container Registry를 사용하면 리포지토리, 이미지, 계층 또는 태그를 [레지스트리 스토리지 한도](container-registry-skus.md#service-tier-features-and-limits) 내에서 필요한 만큼 얼마든지 만들 수 있습니다. 

저장소 및 태그 수가 많으면 레지스트리 성능에 영향을 줄 수 있습니다. 레지스트리 유지 관리 루틴의 일부로 사용되지 않는 리포지토리, 태그, 이미지를 주기적으로 삭제하고 필요에 따라 태그가 없는 매니페스트에 대한 [보존 정책을](container-registry-retention-policy.md) 설정합니다. 리포지토리, 이미지, 태그 등 삭제된 레지스트리 리소스는 삭제 후 복구할 수 ‘없습니다’. 레지스트리 리소스를 삭제하는 방법에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조하세요.

## <a name="storage-cost"></a>스토리지 비용

가격 책정에 대한 자세한 내용은 [Azure Container Registry 가격 책정][pricing]을 참조하세요.

## <a name="next-steps"></a>다음 단계

기본, 표준, 프리미엄 컨테이너 레지스트리에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
