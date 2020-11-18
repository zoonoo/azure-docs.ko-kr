---
title: Azure 용 Data Lake Storage 및 WANdisco 사용 중인 데이터 플랫폼 (미리 보기)
description: Azure 용 WANdisco 사용 중인 데이터 Platform을 사용 하 여 온-프레미스 Hadoop 데이터를 Azure Data Lake Storage Gen2로 마이그레이션합니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/06/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 9da6ea7abf57ffecc900a6dbef065a8c6b123e61
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94810998"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Azure 용 WANdisco 사용 중인 데이터 Platform (미리 보기)을 사용 하 여 까다로운 마이그레이션 요구 사항 충족

[Azure 용 WANdisco 사용 중인 데이터 Platform](https://docs.wandisco.com/live-data-platform/docs/landing/)을 사용 하 여 온-프레미스 Hadoop 데이터를 Azure Data Lake Storage Gen2로 마이그레이션합니다. 이 플랫폼은 응용 프로그램 가동 중지 시간을 제거 하 고, 데이터 손실 가능성을 제거 하 고, 작업이 온-프레미스로 계속 되는 동안에도 데이터 일관성을 유지할 필요가 없습니다.  

> [!NOTE]
> Azure 용 WANdisco 사용 중인 데이터 Platform은 공개 미리 보기로 제공 됩니다. 지역별 가용성은 [지원 되는 지역](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)을 참조 하세요.

이 플랫폼은 온-프레미스 환경에서 Azure storage로 적극적으로 사용 되는 데이터를 마이그레이션하기 위해 [azure에 대 한 사용 중인 데이터 Migrator](https://www.wandisco.com/products/livedata-migrator-for-azure) 및 수정 된 모든 데이터 또는 수집 데이터를 일관 되 게 복제 하는 [Azure 용 사용 중인 데이터 평면](https://www.wandisco.com/products/livedata-plane-for-azure) 이라는 두 가지 서비스로 구성 됩니다. 

> [!div class="mx-imgBorder"]
> ![라이브 데이터 플랫폼 개요 그림](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Azure Portal 및 Azure CLI를 사용 하 여 두 서비스를 관리할 수 있으며, 다른 모든 Azure 서비스와 동일한 요금제 및 종 량 제 청구 모델을 따릅니다. Azure 소비에 대 한 사용 중인 데이터 플랫폼은 동일한 월간 Azure 청구서에 표시 되며, 사용 현황을 추적 하 고 모니터링 하는 일관 되 고 편리한 방법을 제공 합니다.

[정적 정보를 Azure Data Box로 복사](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster)하거나 [distcp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)와 같은 Hadoop 도구를 사용 하 여 _오프 라인_ 으로 데이터를 마이그레이션하는 것과는 달리, Azure 용 WANdisco 사용 중인 데이터를 사용 하 여 _온라인_ 마이그레이션 중에 비즈니스 시스템의 전체 작업을 유지할 수 있습니다. 데이터를 Azure로 이동 하는 동안에도 빅 데이터 환경을 계속 작동 합니다.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Azure 용 WANdisco 사용 중인 데이터 Platform의 주요 기능

[Azure 용 WANdisco 사용 중인 데이터 Platform](https://docs.wandisco.com/live-data-platform/docs/landing/) 은 고유한 광역 네트워크 지원 합의 엔진을 사용 하 여 데이터 일관성을 유지 하 고, 응용 프로그램에서 복제 중인 데이터를 계속 해 서 수정할 수 있는 반면, 대규모로 데이터 복제를 수행 합니다.  

플랫폼의 주요 기능에는 다음이 포함 됩니다.

- **데이터 일관성**: 환경 간에 대용량 데이터 볼륨을 마이그레이션하는 문제를 해결 하 고 이러한 데이터를 지속적으로 변경 되는 동안에도 저장소 시스템 처리량 마이그레이션 간에 일관 되 게 유지 합니다. Azure에서 직접 WANdisco의 고유한 광역 네트워크 지원 합의 엔진을 사용 하 여 데이터 일관성을 유지 하 고 데이터 변경 전체에 걸쳐 일관성을 유지 하면서 데이터를 마이그레이션합니다.

- **작업 유지 관리**: 응용 프로그램에서 마이그레이션 중에 데이터를 계속 만들고, 수정 하 고, 읽고, 삭제할 수 있으므로 중요 한 데이터를 Azure로 마이그레이션하기 위해 비즈니스 운영을 방해 하거나 중단 기간을 도입할 필요가 없습니다. 응용 프로그램, 분석 인프라, 수집 작업 및 기타 처리를 계속 합니다.

- **결과 유효성 검사**: Azure로 마이그레이션한 후 데이터를 효과적으로 사용할 수 있는 종단 간 유효성 검사는 프로덕션 응용 프로그램 워크 로드를 실행 해야 합니다. 변경 내용이 마이그레이션의 원본 또는 대상에서 발생 하는지 여부에 관계 없이 데이터 일관성을 유지 하 여 데이터 확산 위험을 일으키지 않고 사용 중인 데이터 서비스만이를 제공 합니다. 위험 없이 응용 프로그램 동작을 테스트 하 고 유효성을 검사 하거나 프로세스 및 시스템을 변경 합니다.

- **복잡성 줄이기**: 자동화를 통해 데이터를 마이그레이션하여 데이터를 복사 하기 위해 예약 된 작업을 만들고 관리할 필요가 없습니다. 선택적 데이터 복제, Hive 메타 데이터, 데이터 보안 및 기밀성을 비롯 하 여 마이그레이션 진행률을 관리 하 고 모니터링 하려면 제어 평면으로 Azure와 심층 통합을 사용 합니다.

- **효율성**: 높은 처리량과 성능을 유지 하 고 빅 데이터 볼륨으로 쉽게 확장할 수 있습니다. 대역폭 소비 제어를 통해 다른 시스템 작업에 영향을 주지 않고 마이그레이션 목표를 충족할 수 있습니다.

## <a name="migrate-big-data-faster-without-risk"></a>위험 없이 큰 데이터를 더 빠르게 마이그레이션

Azure 용 WANdisco 사용 중인 데이터 Platform의 첫 번째 서비스는 [azure에 대 한 사용 중인 데이터 Migrator](https://www.wandisco.com/products/livedata-migrator-for-azure)입니다. 온-프레미스 환경에서 Azure storage로 적극적으로 사용 되는 데이터를 마이그레이션하기 위한 솔루션입니다. Azure에 대 한 사용 중인 데이터 Migrator는 완전히 Azure Portal 또는 Azure CLI에서 프로 비전 되 고 관리 되며, 데이터 마이그레이션을 즉시 시작 하기 위해 구성 변경, 응용 프로그램 수정 또는 서비스 다시 시작 없이 온-프레미스의 Hadoop 클러스터와 함께 작동 합니다.

> [!div class="mx-imgBorder"]
> ![Azure 아키텍처용 사용 중인 데이터 Migrator](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

빅 데이터 마이그레이션은 복잡 하 고 어려울 수 있습니다. 비즈니스 작업을 중단 하지 않고 정보를 이동 하는 것은 오프 라인 데이터 복사 기술로 페타바이트 수 없었습니다. [사용 중인 데이터 Migrator For Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) 는 간단한 배포를 제공 하며, 응용 프로그램에서 마이그레이션되는 데이터를 읽고, 쓰고, 수정 하는 동안 연속 데이터 마이그레이션 및 복제를 사용 하 여 사용 중인 데이터 서비스를 설정할 수 있습니다.

마이그레이션을 수행 하는 과정은 다음과 같은 세 단계로 간단 합니다.

1. Azure Portal에서 온-프레미스 Hadoop 클러스터로 사용 중인 데이터 Migrator 인스턴스를 프로 비전 합니다. 클러스터 변경 또는 가동 중지 시간이 필요 하지 않으며 응용 프로그램은 계속 작동할 수 있습니다.

   > [!div class="mx-imgBorder"]
   >![사용 중인 데이터 Migrator 인스턴스 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. 대상 Azure Data Lake Storage Gen2 사용 저장소 계정을 정의 합니다.

   > [!div class="mx-imgBorder"]
   >![사용 중인 데이터 Migrator target 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. 마이그레이션할 데이터의 위치 (예:)를 정의 `/user/hive/warehouse` 하 고 마이그레이션을 시작 합니다.

   > [!div class="mx-imgBorder"]
   > ![사용 중인 데이터 Migrator 마이그레이션 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Azure CLI 및 Azure Portal를 비롯 한 표준 Azure 도구를 통해 마이그레이션 진행 상황을 모니터링 하 고 전체에서 온-프레미스 환경을 계속 사용 합니다. 시작 하기 전에 이러한 [필수 구성 요소](https://docs.wandisco.com/live-data-platform/docs/prereq/)를 검토 합니다.

## <a name="replicate-data-under-active-change"></a>활성 변경에서 데이터 복제

온-프레미스 데이터 레이크 Azure로의 대규모 마이그레이션에는 응용 프로그램 테스트 및 유효성 검사가 필요 합니다. 쉽게 조정할 수 없는 여러 가지 원인을 만드는 데이터 변경 사항을 도입 하는 위험 없이이 작업을 수행할 수 있는 것은 위험을 제거 하 고 Azure로 이동 하는 비용을 최소화 하는 데 매우 중요 합니다. [Azure의 사용 중인 데이터 평면은](https://www.wandisco.com/products/livedata-plane-for-azure) WANdisco의 조정 엔진 기술을 사용 하 여 이러한 문제를 해결 합니다.

> [!div class="mx-imgBorder"]
> ![Azure 아키텍처에 대 한 사용 중인 데이터 평면](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

초기 마이그레이션 후 Azure 용 사용 중인 데이터 평면을 사용 하 여 온-프레미스 Hadoop 클러스터 및 Azure storage에서 데이터 일관성을 유지 합니다.

1. Azure Portal에서 시작 하 여 azure 온-프레미스 및 Azure에 대 한 사용 중인 데이터 평면을 프로 비전 합니다. 응용 프로그램을 변경할 필요가 없습니다.
2. 일관 되 게 유지 하려는 데이터 위치를 포함 하는 복제 규칙을 구성 합니다 (예:) `/user/contoso/sales/region/WA` .
3. 두 위치의 데이터에 액세스 하 고 수정 하는 응용 프로그램을 필요에 따라 Hadoop 호환 파일 시스템으로 실행 합니다.

Azure의 사용 중인 데이터 평면은 클러스터 작업 또는 응용 프로그램 성능에 대 한 상당한 오버 헤드를 부과 하지 않고 데이터를 일관 되 게 유지 합니다. 모든 변경 내용이 일관 되 게 복제 되는 동안 데이터를 수정 하거나 수집 합니다.

## <a name="next-steps"></a>다음 단계

- Azure 용 [azure 용 사용 중인 데이터 Platform](https://docs.wandisco.com/live-data-platform/docs/landing/) 은 다른 azure 리소스와 마찬가지로 사용 되며 지금 미리 보기에서 사용할 수 있습니다. 

- Azure에 대 한 사용 중인 데이터 Migrator를 사용 하 여 [필수 구성 요소](https://docs.wandisco.com/live-data-platform/docs/prereq/)를 이해 하 고, 마이그레이션을 계획 하 고, 대규모 마이그레이션을 신속 하 게 완료 합니다.

- [HDFS 샌드박스](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)를 사용 하 여 온-프레미스 Hadoop 클러스터가 없어도 사용 중인 데이터 Migrator를 사용해 보세요.

## <a name="see-also"></a>참조

- [Azure Marketplace Azure 사용 중인 데이터 Migrator](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Azure Marketplace의 Azure에 대 한 사용 중인 데이터 평면](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [Azure 요금제 및 가격 책정에 대 한 사용 중인 데이터 Migrator](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Azure 요금제 및 가격 책정에 대 한 사용 중인 데이터 평면](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [Azure에 대 한 질문과 대답 사용 중인 데이터 플랫폼](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Azure 용 사용 중인 데이터 Platform의 알려진 문제](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)