---
title: 컨테이너 이미지 스토리지
description: 보안, 중복성 및 용량을 포함하여 Docker 컨테이너 이미지가 Azure Container Registry에 저장되는 방식에 대한 세부 정보입니다.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85214063"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Azure Container Registry의 컨테이너 이미지 스토리지

모든 [기본, 표준 및 프리미엄](container-registry-skus.md) Azure Container Registry는 이미지 데이터 보안을 위한 미사용 시 암호화 및 이미지 데이터 보호를 위한 지역 중복과 같은 고급 Azure Storage 기능을 활용합니다. 다음 섹션에서는 ACR(Azure Container Registry)의 이미지 스토리지 기능과 제한을 둘 다 설명합니다.

## <a name="encryption-at-rest"></a>미사용 시 암호화

레지스트리의 모든 컨테이너 이미지는 미사용 시 암호화됩니다. Azure는 저장하기 전에 이미지를 자동으로 암호화하고, 애플리케이션 및 서비스가 이미지를 끌어올 때 즉석에서 이미지의 암호를 해독합니다. 필요에 따라 [고객이 관리](container-registry-customer-managed-keys.md)하는 키를 사용 하 여 추가 암호화 계층을 적용 합니다.

## <a name="geo-redundant-storage"></a>지역 중복 스토리지

Azure는 지역 중복 스토리지 체계를 사용하여 컨테이너 이미지의 손실을 방지합니다. Azure Container Registry는 컨테이너 이미지를 지리적으로 떨어진 여러 데이터 센터에 자동으로 복제하여 지역 스토리지 오류가 발생할 경우, 손실을 방지합니다.

## <a name="geo-replication"></a>지역에서 복제

고가용성 보장이 필요한 시나리오의 경우 프리미엄 레지스트리의 [지역에서 복제](container-registry-geo-replication.md) 기능을 사용하는 것이 좋습니다. 지역에서 복제는 스토리지 오류뿐만 아니라 *전체* 지역 오류가 발생할 경우, 레지스트리에 대한 액세스 손실을 방지합니다. 지역에서 복제는 분산 개발 또는 배포 시나리오에서 빠른 푸시 및 풀을 위한 네트워크 근접 이미지 스토리지와 같은 다른 이점도 제공합니다.

## <a name="scalable-storage"></a>확장 가능한 저장소

Azure Container Registry를 사용 하 여 필요한 만큼의 리포지토리, 이미지, 계층 또는 태그를 [레지스트리 저장소 제한](container-registry-skus.md#service-tier-features-and-limits)까지 만들 수 있습니다. 

저장소 및 태그 수가 매우 많으면 레지스트리 성능에 영향을 줄 수 있습니다. 레지스트리 유지 관리 루틴의 일부로 사용 하지 않는 리포지토리, 태그 및 이미지를 주기적으로 삭제 하 고, 필요에 따라 태그가 없는 매니페스트에 대 한 [보존 정책을](container-registry-retention-policy.md) 설정 합니다. 삭제 한 후에는 리포지토리, 이미지 및 태그와 같은 삭제 된 레지스트리 리소스를 복구할 *수 없습니다* . 레지스트리 리소스를 삭제하는 방법에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조하세요.

## <a name="storage-cost"></a>스토리지 비용

가격 책정에 대한 자세한 내용은 [Azure Container Registry 가격 책정][pricing]을 참조하세요.

## <a name="next-steps"></a>다음 단계

기본, 표준, 프리미엄 컨테이너 레지스트리에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
