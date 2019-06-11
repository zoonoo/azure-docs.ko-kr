---
title: Azure 구독 제한 및 할당량
description: 일반적인 Azure 구독 및 서비스 제한, 할당량 및 제약 조건 목록을 제공합니다. 이 문서는 최대값과 함께 제한을 늘리는 방법에 대 한 정보를 포함 합니다.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 88c40021ff2e974ba16b3b3506ee54b45de90fda
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427604"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 구독 및 서비스 제한, 할당량 및 제약 조건
이 문서는 때때로 할당량이라고도 하는 가장 일반적인 Microsoft Azure 제한의 일부를 나열합니다. 현재 이 문서에서는 일부 Azure 서비스에 대해 다룹니다. 시간이 지남에 따라 목록 확장 되며 더 많은 서비스에 맞게 업데이트 합니다.

Azure 가격 책정에 대 한 자세한 내용은 참조 하세요 [가격 책정 개요 Azure](https://azure.microsoft.com/pricing/)합니다. 사용 하 여 비용을 예측할 수 있습니다, 합니다 [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)합니다. 또한 이동할 수 있습니다 특정 서비스에 대 한 가격 책정 세부 정보 페이지 예를 들어 [Windows Vm](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)합니다. 비용 관리에 대한 팁은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing/billing-getting-started.md)를 참조하세요.

> [!NOTE]
> 제한 또는 기본 제한 초과 하는 할당량을 높이려는 경우 [무료로 온라인 고객 지원 요청을 개설](azure-resource-manager/resource-manager-quota-errors.md)합니다. 다음 표에 표시 된 최대 제한 값 이상으로 제한은 높일 수 없습니다. 최대 한도 열이 없는 경우 리소스에 조정 가능한 제한이 없습니다.
>
> [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p) 제한 또는 할당량 증가 대상이 되지 않습니다. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. 자세한 내용은 [Azure 무료 평가판 구독을 종 량 제 구독으로 업그레이드](billing/billing-upgrade-azure-subscription.md) 하며 [무료 평가판 구독 FAQ](https://azure.microsoft.com/free/free-account-faq)합니다.
>

## <a name="limits-and-azure-resource-manager"></a>제한 및 Azure Resource Manager
이제 단일 Azure 리소스 그룹에 여러 Azure 리소스를 결합 하는 것이 불가능 합니다. 리소스 그룹을 사용 하는 경우 전역 이었던 제한이 Azure Resource Manager를 사용한 지역 수준에서 관리 합니다. Azure 리소스 그룹에 대 한 자세한 내용은 참조 하세요. [Azure Resource Manager 개요](azure-resource-manager/resource-group-overview.md)합니다.

제한의 다음 목록에서 새 테이블을 Azure Resource Manager를 사용 하는 경우 제한에 차이 반영 합니다. 예를 들어, 한 **구독 제한** 테이블 및 **구독 제한-Azure Resource Manager** 테이블입니다. 제한 시나리오 모두에 적용 될 때 첫 번째 테이블에만 표시 됩니다. 별도로 지정하지 않으면 제한은 모든 지역에 걸쳐 전역으로 적용됩니다.

> [!NOTE]
> Azure 리소스 그룹의 리소스에 대 한 할당량은 서비스 관리 할당량이 지역 하지 구독 당 구독에 액세스할 수 있습니다. vCPU 할당량을 한 예로 살펴보겠습니다. Vcpu 지 원하는 할당량 증가 요청 하는 지역에서 사용 하려면 얼마나 많은 Vcpu를 결정 해야 합니다. 금액 및 원하는 지역에 대 한 Azure 리소스 그룹 vCPU 할당량에 대 한 특정 요청을 확인 합니다. 유럽 서 부에서 30 개의 Vcpu를 사용 하 여 응용 프로그램 실행 해야 할 경우 유럽 서 부에서 30 개의 Vcpu를 구체적으로 요청 합니다. 유럽 서 부에 30 개의 vCPU 할당량이-다른 지역에 vCPU 할당량 증가 되지 않습니다.
> <!-- -->
> 따라서 어떤 한 지역에서 워크 로드에 대 한 이어야 하는 Azure 리소스 그룹 할당량 합니다 결정 합니다. 다음에 배포 하려는 각 지역에 해당 금액을 요청 합니다. 특정 지역에 대 한 현재 할당량을 결정 하는 방법에 대 한 도움말을 참조 하세요 [관련 배포 문제 해결](resource-manager-common-deployment-errors.md)합니다.
>
>

## <a name="service-specific-limits"></a>서비스 특정 제한
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning 서비스](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [컨테이너 레지스트리](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [첫 번째 관문 서비스](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub Device Provisioning Service](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [네트워킹](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [공용 IP 주소](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Notification Hubs](#notification-hubs-limits)
* [리소스 그룹](#resource-group-limits)
* [역할 기반 액세스 제어](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [사이트 복구](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple 시스템](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [구독](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [가상 머신 확장 집합](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>구독 제한
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>구독 제한-Azure 서비스 관리 (클래식 배포 모델)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>구독 제한 - Azure Resource Manager
다음과 같은 제한이 Azure Resource Manager 및 Azure 리소스 그룹을 사용할 때 적용 됩니다. Azure Resource Manager를 변경 하지 않은 한도 표시 되지 않습니다. 이러한 제한 이전 표를 참조 하세요.

Resource Manager API 읽기 및 쓰기 제한은 [Resource Manager 요청 제한](resource-manager-request-limits.md)을 참조하세요.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>리소스 그룹 제한
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines 제한
#### <a name="virtual-machines-limits"></a>Virtual Machines 제한
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines 제한 - Azure 리소스 관리자
다음과 같은 제한이 Azure Resource Manager 및 Azure 리소스 그룹을 사용할 때 적용 됩니다. Azure Resource Manager를 변경 하지 않은 한도 표시 되지 않습니다. 이러한 제한 이전 표를 참조 하세요.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>이미지 갤러리 제한 공유

공유 이미지 갤러리를 사용 하 여 리소스를 배포 하기 위한 구독 당 제한이 있습니다.
- 지역당 구독 당 100 공유 이미지 갤러리
- 지역당 구독 당 1,000 이미지 정의
- 지역당 구독 당 10,000 이미지 버전

### <a name="virtual-machine-scale-sets-limits"></a>가상 머신 확장 집합 제한
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances 제한
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry 제한
다음 표에서는 기본, 표준 및 프리미엄 [서비스 계층](./container-registry/container-registry-skus.md)의 기능 및 제한에 대해 자세히 설명합니다.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service 제한
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Azure Machine Learning 서비스 제한
최신 Azure Machine Learning Compute 할당량 값에서 찾을 수 있습니다는 [Azure Machine Learning의 할당량 페이지](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>네트워킹 제한
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute 제한
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway 제한

다음 표는 달리 언급하지 않는 한 v1, v2, 표준 및 WAF SKU에 적용됩니다.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher 제한
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager 제한
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS 제한
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure Firewall 제한
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Azure Front Door Service 제한
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>저장소 제한
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

저장소 계정 제한에 대 한 자세한 내용은 참조 하세요. [Azure Storage 확장성 및 성능 목표](storage/common/storage-scalability-targets.md)합니다.

#### <a name="storage-resource-provider-limits"></a>Storage 리소스 공급자 제한 

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob Storage 용량 한도
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files 한도
Azure Files 한도에 대 한 자세한 내용은 참조 하세요. [Azure Files 확장성 및 성능 목표](storage/files/storage-files-scale-targets.md)합니다.

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure 파일 동기화 한도
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue 저장소 용량 한도
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table Storage 용량 한도
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>가상 머신 디스크 제한
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [가상 머신 크기](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)합니다.

#### <a name="managed-virtual-machine-disks"></a>관리되는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services 제한
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>App Service 제한
다음 App Service 제한에는 Web Apps, Mobile Apps 및 API Apps에 대한 제한이 포함됩니다.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>함수 제한
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Scheduler 제한
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch 제한
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services 제한
다음 표에서 Azure BizTalk Services에 대 한 제한을 보여 줍니다.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 제한
Azure Cosmos DB 제한 참조 [Azure Cosmos DB의 제한 사항](cosmos-db/concepts-limits.md)합니다.

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Azure Database for MySQL 제한은 [Azure Database for MySQL의 제한 사항](mysql/concepts-limits.md)을 참조하세요.

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Azure Database for PostgreSQL 제한은 [Azure Database for PostgreSQL의 제한 사항](postgresql/concepts-limits.md)을 참조하세요.

### <a name="azure-search-limits"></a>Azure Search 제한
가격 책정 계층은 검색 서비스의 용량 및 제한을 결정합니다. 계층은 다음을 포함합니다.

* **무료** 다른 Azure 구독자와 공유 하는 다중 테 넌 트 서비스를 평가 및 소규모 개발 프로젝트에 대 한 것입니다.
* **기본** 은 높은 가용성의 쿼리 작업에 대한 최대 3개의 복제본과 함께 프로덕션 워크로드 전용 컴퓨팅 리소스를 더 작은 규모로 제공합니다.
* **표준**, S1, S2, S3, 포함 및 S3 High Density, 더 큰 프로덕션 워크 로드입니다. 여러 표준 계층 내는 수준이 있으므로 워크 로드 프로필에 가장 적합 한 리소스 구성을 선택할 수 있습니다.

**구독당 제한**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**검색 서비스당 제한**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

문서 크기, 초당 쿼리, 키, 요청 및 응답을 비롯한 좀 더 자세한 수준의 제한 사항에 대한 자세한 내용은 [Azure Search의 서비스 제한 사항](search/search-limits-quotas-capacity.md)을 참조하세요.

### <a name="media-services-limits"></a>Media Services 제한
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network 제한
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services 제한
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitor-limits"></a>모니터 제한
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hubs-limits"></a>Notification Hubs 제한
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs 제한
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus 제한
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>IoT Hub 제한
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service 제한
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>데이터 팩터리 제한
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Data Lake Analytics 제한
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Data Lake Store 제한
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Database Migration Service 제한
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Stream Analytics 제한
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Active Directory 제한
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Event Grid 제한
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps 제한
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Policy 제한
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple 시스템 제한
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="log-analytics-limits"></a>Log Analytics 한도
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Backup 제한
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Azure SignalR Service 제한
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>사이트 복구 제한
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Application Insights 제한
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>API Management 제한
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis 제한
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>키 값 제한
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-factor Authentication 제한
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation 한도
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager 제한
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>역할 기반 액세스 제어 한도
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database 제한
SQL Database 제한에 대 한 참조 [단일 데이터베이스에 대 한 SQL Database 리소스 제한](sql-database/sql-database-vcore-resource-limits-single-databases.md)하십시오 [탄력적 풀 및 풀링된 데이터베이스에 대 한 SQL Database 리소스 제한](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), 및 [SQL Database 리소스 제한 관리 되는 인스턴스](sql-database/sql-database-managed-instance-resource-limits.md)합니다.

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse 제한
SQL Data Warehouse 제한 참조 [SQL Data Warehouse 리소스 제한](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)합니다.

## <a name="see-also"></a>참고 항목
- [Azure 제한 및 증가 이해](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Azure 가상 컴퓨터 및 클라우드 서비스 크기](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure Cloud Services 크기](cloud-services/cloud-services-sizes-specs.md)
