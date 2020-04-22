---
title: Azure 구독 제한 및 할당량
description: 일반적인 Azure 구독 및 서비스 제한, 할당량 및 제약 조건 목록을 제공합니다. 이 문서에는 최대 값과 함께 제한을 늘리는 방법에 대한 정보가 포함되어 있습니다.
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 00bd85febe34a7bd9b4e993d5697fa810347711b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768744"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 구독 및 서비스 제한, 할당량 및 제약 조건

이 문서는 때때로 할당량이라고도 하는 가장 일반적인 Microsoft Azure 제한의 일부를 나열합니다.

Azure 가격 책정에 대해 자세히 알아보려면 [Azure 가격 개요를](https://azure.microsoft.com/pricing/)참조하십시오. 가격 [계산기를](https://azure.microsoft.com/pricing/calculator/)사용하여 비용을 예측할 수 있습니다. 특정 서비스에 대한 가격 세부 정보 페이지(예: [Windows VM)로](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)이동하여 확인할 수도 있습니다. 비용 관리에 대한 팁은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](../../billing/billing-getting-started.md)를 참조하세요.

## <a name="managing-limits"></a>한도 관리

> [!NOTE]
> 일부 서비스에는 조정 가능한 제한이 있습니다.
>
> 서비스에 조정 가능한 제한이 없는 경우 다음 표에서는 헤더 **제한**을 사용합니다. 이러한 경우 기본값과 최대 한도는 동일합니다.
>
> 제한을 조정할 수 있는 경우 **테이블에는 기본 제한** 및 **최대 제한** 헤더가 포함됩니다. 제한은 기본 한도를 초과할 수 있지만 최대 한도를 초과할 수는 없습니다.
>
> 한도 또는 할당량을 기본 한도 이상으로 높이려면 [온라인 고객 지원 요청을 무료로 엽니다.](../templates/error-resource-quota.md)

[무료 평가판 구독은](https://azure.microsoft.com/offers/ms-azr-0044p) 제한 또는 할당량 증가를 받을 수 없습니다. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 무료 평가판 구독을 종량제 구독](../../billing/billing-upgrade-azure-subscription.md) 및 무료 [평가판 구독 FAQ로](https://azure.microsoft.com/free/free-account-faq)업그레이드를 참조하십시오.

일부 제한은 지역 수준에서 관리됩니다.

vCPU 할당량을 한 예로 살펴보겠습니다. vCPU를 지원하여 할당량 증가를 요청하려면 어떤 지역에서 사용할 vCPU 수를 결정해야 합니다. 그런 다음 원하는 금액 및 지역에 대해 Azure 리소스 그룹 vCPU 할당량에 대한 특정 요청을 합니다. 서유럽에서 30개의 vCPU를 사용하여 응용 프로그램을 실행해야 하는 경우 서유럽에서 30개의 vCPU를 구체적으로 요청합니다. vCPU 할당량은 다른 지역에서는 증가하지 않으며 서유럽만 30vCPU 할당량을 가합니다.

따라서 한 리전에서 워크로드에 대해 Azure 리소스 그룹 할당량을 결정합니다. 그런 다음 배포하려는 각 리전에서 해당 금액을 요청합니다. 특정 지역에 대한 현재 할당량을 확인하는 방법에 대한 도움말은 [리소스 할당량에 대한 오류 해결을](../templates/error-resource-quota.md)참조하십시오.

## <a name="general-limits"></a>일반 한도

리소스 이름에 대한 제한은 [Azure 리소스에 대한 명명 규칙 및 제한을](resource-name-rules.md)참조하십시오.

Resource Manager API 읽기 및 쓰기 제한은 [Resource Manager 요청 제한](request-limits-and-throttling.md)을 참조하세요.

### <a name="management-group-limits"></a>관리 그룹 제한

관리 [그룹에는](../../governance/management-groups/overview.md)다음 제한이 적용됩니다.

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>구독 제한

Azure 리소스 관리자 및 Azure 리소스 그룹을 사용할 때 다음 제한이 적용됩니다.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>리소스 그룹 제한

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory 제한

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API Management 제한

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App Service 제한

다음 App Service 제한에는 Web Apps, Mobile Apps 및 API Apps에 대한 제한이 포함됩니다.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automation 한도

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis 제한

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure 클라우드 서비스 제한

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure 인지 검색 제한

가격 책정 계층은 검색 서비스의 용량 및 제한을 결정합니다. 계층은 다음을 포함합니다.

* 다른 Azure 구독자와 공유되는 **무료** 다중 테넌트 서비스는 평가 및 소규모 개발 프로젝트를 위한 것입니다.
* **기본** 은 높은 가용성의 쿼리 작업에 대한 최대 3개의 복제본과 함께 프로덕션 워크로드 전용 컴퓨팅 리소스를 더 작은 규모로 제공합니다.
* S1, S2, S3 및 S3 고밀도를 포함하는 **표준**은 더 큰 프로덕션 워크로드를 위한 것입니다. 워크로드 프로필에 가장 적합한 리소스 구성을 선택할 수 있도록 표준 계층 내에 여러 수준이 있습니다.

**구독당 제한**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**검색 서비스당 제한**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

문서 크기, 초당 쿼리, 키, 요청 및 응답과 같은 보다 세부적인 수준의 제한에 대한 자세한 내용은 [Azure Cognitive Search의 서비스 제한을](../../search/search-limits-quotas-capacity.md)참조하십시오.

## <a name="azure-cognitive-services-limits"></a>Azure 코그너티브 서비스 제한

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 제한

Azure 코스모스 DB 제한에 대 한 [Azure 코스모스 DB의 제한을](../../cosmos-db/concepts-limits.md)참조 합니다.

## <a name="azure-data-explorer-limits"></a>Azure 데이터 탐색기 제한

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL 제한은 [Azure Database for MySQL의 제한 사항](../../mysql/concepts-limits.md)을 참조하세요.

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Azure Database for PostgreSQL 제한은 [Azure Database for PostgreSQL의 제한 사항](../../postgresql/concepts-limits.md)을 참조하세요.

## <a name="azure-functions-limits"></a>Azure 함수 제한

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

## <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes 서비스 제한

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure 기계 학습 제한

Azure 기계 학습 계산 할당량에 대한 최신 값은 [Azure 기계 학습 할당량 페이지에서](../../machine-learning/how-to-manage-quotas.md) 찾을 수 있습니다.

## <a name="azure-maps-limits"></a>Azure Maps 제한

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure 모니터 제한

### <a name="alerts"></a>경고

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>작업 그룹

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="log-queries-and-language"></a>로그 쿼리 및 언어

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics 작업 영역

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure Policy 제한

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Azure 신호R 서비스 제한

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Backup 제한

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch 제한

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>클래식 배포 모델 제한

Azure 리소스 관리자 배포 모델 대신 클래식 배포 모델을 사용하는 경우 다음 제한이 적용됩니다.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances 제한

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry 제한

다음 표에서는 기본, 표준 및 프리미엄 서비스 계층의 기능 및 제한에 대해 자세히 [설명합니다.](../../container-registry/container-registry-skus.md)

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>콘텐츠 전송 네트워크 제한

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>데이터 팩터리 제한

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics 제한

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-store-limits"></a>Data Lake Store 제한

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>데이터 공유 한도

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Database Migration Service 제한

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="event-grid-limits"></a>이벤트 그리드 제한

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Event Hubs 제한

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="identity-manager-limits"></a>ID 관리자 제한

[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

## <a name="iot-central-limits"></a>IoT 중앙 제한
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub 제한

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service 제한

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>키 값 제한

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="media-services-limits"></a>Media Services 제한

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2(레거시)

미디어 서비스 v2(레거시)와 관련된 제한은 [미디어 서비스 v2(레거시)를](https://docs.microsoft.com/azure/media-services/previous/media-services-quotas-and-limitations) 참조하십시오.

## <a name="mobile-services-limits"></a>Mobile Services 제한

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>다단계 인증 한도

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>네트워킹 제한

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>익스프레스루트 제한

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-wan-limits"></a>가상 WAN 제한

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Application Gateway 제한

다음 표는 달리 언급하지 않는 한 v1, v2, 표준 및 WAF SKU에 적용됩니다.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Network Watcher 제한

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link 제한

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

### <a name="traffic-manager-limits"></a>Traffic Manager 제한

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure 요새 제한

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS 제한

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure Firewall 제한

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure Front Door Service 제한

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>알림 허브 제한

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="role-based-access-control-limits"></a>역할 기반 액세스 제어 한도

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Service Bus 제한

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>사이트 복구 제한

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL Database 제한

SQL Database 제한에 대 한 [단일 데이터베이스에 대 한 SQL Database 리소스 제한,](../../sql-database/sql-database-vcore-resource-limits-single-databases.md) [탄력적 풀 및 풀된 데이터베이스에 대 한 SQL Database 리소스 제한](../../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)및 [관리되는 인스턴스에 대 한 SQL Database 리소스 제한을](../../sql-database/sql-database-managed-instance-resource-limits.md)참조 합니다.

## <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse 제한

SQL 데이터 웨어하우스 제한은 [SQL 데이터 웨어하우스 리소스 제한을](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)참조하십시오.

## <a name="storage-limits"></a>스토리지 제한

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

표준 저장소 계정의 한도에 대한 자세한 내용은 [표준 저장소 계정에 대한 확장성 목표를](../../storage/common/scalability-targets-standard-account.md)참조하십시오.

### <a name="storage-resource-provider-limits"></a>Storage 리소스 공급자 제한

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob Storage 용량 한도

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure Files 한도

Azure 파일 제한에 대한 자세한 내용은 [Azure 파일 확장성 및 성능 대상을](../../storage/files/storage-files-scale-targets.md)참조하십시오.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Azure 파일 동기화 한도

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Azure Queue storage 용량 한도

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Azure Table Storage 용량 한도

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>가상 머신 디스크 제한

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [가상 컴퓨터 크기를 참조하십시오.](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="managed-virtual-machine-disks"></a>관리되는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple 시스템 제한

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Stream Analytics 제한

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Virtual Machines 제한

### <a name="virtual-machines-limits"></a>Virtual Machines 제한

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines 제한 - Azure 리소스 관리자

Azure 리소스 관리자 및 Azure 리소스 그룹을 사용할 때 다음 제한이 적용됩니다.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>공유 이미지 갤러리 제한

공유 이미지 갤러리를 사용하여 리소스를 배포하는 경우 구독당 제한이 있습니다.

- 지역별 구독당 공유 이미지 갤러리 100개
- 리전당 구독당 1,000개의 이미지 정의
- 리전당 구독당 10,000개의 이미지 버전

## <a name="virtual-machine-scale-sets-limits"></a>가상 머신 스케일 세트 제한

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>참조

* [Azure 제한 및 증가 이해](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Azure용 가상 컴퓨터 및 클라우드 서비스 크기](../../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 클라우드 서비스에 대한 크기](../../cloud-services/cloud-services-sizes-specs.md)
* [Azure 리소스에 대한 명명 규칙 및 제한 사항](resource-name-rules.md)
