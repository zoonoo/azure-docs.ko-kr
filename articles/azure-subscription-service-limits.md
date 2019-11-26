---
title: Azure 구독 제한 및 할당량
description: 일반적인 Azure 구독 및 서비스 제한, 할당량 및 제약 조건 목록을 제공합니다. This article includes information on how to increase limits along with maximum values.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: cost-management-billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 08c459a3c32b44df2d9e5cf783087dd34d660292
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463320"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure 구독 및 서비스 한도, 할당량 및 제약 조건
이 문서는 때때로 할당량이라고도 하는 가장 일반적인 Microsoft Azure 제한의 일부를 나열합니다. 현재 이 문서에서는 일부 Azure 서비스에 대해 다룹니다. Over time, the list will be expanded and updated to cover more services.

To learn more about Azure pricing, see [Azure pricing overview](https://azure.microsoft.com/pricing/). There, you can estimate your costs by using the [pricing calculator](https://azure.microsoft.com/pricing/calculator/). You also can go to the pricing details page for a particular service, for example, [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). 비용 관리에 대한 팁은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing/billing-getting-started.md)를 참조하세요.

> [!NOTE]
> If you want to raise the limit or quota above the default limit, [open an online customer support request at no charge](azure-resource-manager/resource-manager-quota-errors.md). The limits can't be raised above the maximum limit value shown in the following tables. If there's no maximum limit column, the resource doesn't have adjustable limits.
>
> [Free Trial subscriptions](https://azure.microsoft.com/offers/ms-azr-0044p) aren't eligible for limit or quota increases. [평가판 구독](https://azure.microsoft.com/offers/ms-azr-0044p)을 사용하는 경우 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 구독으로 업그레이드할 수 있습니다. For more information, see [Upgrade your Azure Free Trial subscription to a Pay-As-You-Go subscription](billing/billing-upgrade-azure-subscription.md) and the [Free Trial subscription FAQ](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Limits and Azure Resource Manager
It's now possible to combine multiple Azure resources into a single Azure resource group. When you use resource groups, limits that once were global become managed at a regional level with Azure Resource Manager. For more information about Azure resource groups, see [Azure Resource Manager overview](azure-resource-manager/resource-group-overview.md).

In the following list of limits, a new table reflects any differences in limits when you use Azure Resource Manager. For example, there's a **Subscription limits** table and a **Subscription limits - Azure Resource Manager** table. When a limit applies to both scenarios, it's only shown in the first table. 별도로 지정하지 않으면 제한은 모든 지역에 걸쳐 전역으로 적용됩니다.

> [!NOTE]
> Quotas for resources in Azure resource groups are per-region accessible by your subscription, not per-subscription as the service management quotas are. vCPU 할당량을 한 예로 살펴보겠습니다. To request a quota increase with support for vCPUs, you must decide how many vCPUs you want to use in which regions. You then make a specific request for Azure resource group vCPU quotas for the amounts and regions that you want. If you need to use 30 vCPUs in West Europe to run your application there, you specifically request 30 vCPUs in West Europe. Your vCPU quota isn't increased in any other region--only West Europe has the 30-vCPU quota.
> <!-- -->
> As a result, decide what your Azure resource group quotas must be for your workload in any one region. Then request that amount in each region into which you want to deploy. For help in how to determine your current quotas for specific regions, see [Troubleshoot deployment issues](resource-manager-common-deployment-errors.md).
>
>

## <a name="service-specific-limits"></a>서비스 특정 제한
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automation](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cognitive Search](#azure-cognitive-search-limits)
* [Azure Cognitive Services](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database for PostgreSQL](#azure-database-for-postgresql)
* [Azure DNS](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Azure Machine Learning](#azure-machine-learning-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Backup](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Container Instances](#container-instances-limits)
* [컨테이너 레지스트리](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [데이터 팩터리](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Central](#iot-central-limits)
* [IoT Hub](#iot-hub-limits)
* [IoT Hub Device Provisioning Service](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [네트워킹](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [Azure Bastion](#azure-bastion-limits)
  * [Azure DNS](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Load Balancer](#load-balancer)
  * [Network Watcher](#network-watcher-limits)
  * [공용 IP 주소](#publicip-address)
  * [Private Link](#private-link-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
  * [Virtual WAN](#virtual-wan-limits)
* [Notification Hubs](#notification-hubs-limits)
* [리소스 그룹](#resource-group-limits)
* [역할 기반 액세스 제어](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [StorSimple 시스템](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [구독](#subscription-limits)
* [Virtual Machines](#virtual-machines-limits)
* [가상 머신 확장 집합](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>구독 제한
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Subscription limits - Azure Service Management (classic deployment model)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>구독 제한 - Azure Resource Manager
The following limits apply when you use Azure Resource Manager and Azure resource groups. Limits that haven't changed with Azure Resource Manager aren't listed. See the previous table for those limits.

Resource Manager API 읽기 및 쓰기 제한은 [Resource Manager 요청 제한](resource-manager-request-limits.md)을 참조하세요.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Resource group limits
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Virtual Machines 제한
#### <a name="virtual-machines-limits"></a>Virtual Machines 제한
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines 제한 - Azure 리소스 관리자
The following limits apply when you use Azure Resource Manager and Azure resource groups.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Shared Image Gallery limits

There are limits, per subscription, for deploying resources using Shared Image Galleries:
- 100 shared image galleries, per subscription, per region
- 1,000 image definitions, per subscription, per region
- 10,000 image versions, per subscription, per region

### <a name="virtual-machine-scale-sets-limits"></a>Virtual machine scale sets limits
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Container Instances 제한
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Container Registry 제한
다음 표에서는 기본, 표준 및 프리미엄 [서비스 계층](./container-registry/container-registry-skus.md)의 기능 및 제한에 대해 자세히 설명합니다.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Azure Kubernetes Service limits
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-limits"></a>Azure Machine Learning limits
The latest values for Azure Machine Learning Compute quotas can be found in the [Azure Machine Learning quota page](../articles/machine-learning/service/how-to-manage-quotas.md)

### <a name="networking-limits"></a>네트워킹 제한
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>ExpressRoute limits
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="virtual-wan-limits"></a>Virtual WAN limits
[!INCLUDE [virtual-wan-limits](../includes/virtual-wan-limits.md)]

#### <a name="application-gateway-limits"></a>Application Gateway 제한

다음 표는 달리 언급하지 않는 한 v1, v2, 표준 및 WAF SKU에 적용됩니다.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Network Watcher 제한
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="private-link-limits"></a>Private Link limits
[!INCLUDE [private-link-limits](../includes/private-link-limits.md)]

#### <a name="traffic-manager-limits"></a>Traffic Manager 제한
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-bastion-limits"></a>Azure Bastion limits
[!INCLUDE [Azure Bastion limits](../includes/bastion-limits.md)]

#### <a name="azure-dns-limits"></a>Azure DNS limits
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Azure Firewall 제한
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Azure Front Door Service 제한
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>스토리지 제한
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

For more information on storage account limits, see [Azure Storage scalability and performance targets](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Storage 리소스 공급자 제한

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Azure Blob Storage 용량 한도
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Azure Files 한도
For more information on Azure Files limits, see [Azure Files scalability and performance targets](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Azure 파일 동기화 한도
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Azure Queue storage 용량 한도
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Azure Table Storage 용량 한도
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>가상 머신 디스크 제한
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

For more information, see [Virtual machine sizes](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>관리되는 가상 컴퓨터 디스크

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 머신 디스크

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Azure Cloud Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services limits
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>App Service 제한
다음 App Service 제한에는 Web Apps, Mobile Apps 및 API Apps에 대한 제한이 포함됩니다.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Functions limits
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Scheduler 제한
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Batch 제한
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>BizTalk Services 제한
The following table shows the limits for Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB 제한
For Azure Cosmos DB limits, see [Limits in Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Azure Database for MySQL 제한은 [Azure Database for MySQL의 제한 사항](mysql/concepts-limits.md)을 참조하세요.

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Azure Database for PostgreSQL 제한은 [Azure Database for PostgreSQL의 제한 사항](postgresql/concepts-limits.md)을 참조하세요.

### <a name="azure-cognitive-search-limits"></a>Azure Cognitive Search limits
가격 책정 계층은 검색 서비스의 용량 및 제한을 결정합니다. 계층은 다음을 포함합니다.

* **Free** multitenant service, shared with other Azure subscribers, is intended for evaluation and small development projects.
* **기본** 은 높은 가용성의 쿼리 작업에 대한 최대 3개의 복제본과 함께 프로덕션 워크로드 전용 컴퓨팅 리소스를 더 작은 규모로 제공합니다.
* **Standard**, which includes S1, S2, S3, and S3 High Density, is for larger production workloads. Multiple levels exist within the Standard tier so that you can choose a resource configuration that best matches your workload profile.

**구독당 제한**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**검색 서비스당 제한**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

To learn more about limits on a more granular level, such as document size, queries per second, keys, requests, and responses, see [Service limits in Azure Cognitive Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Media Services 제한
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Content Delivery Network limits
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Mobile Services 제한
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Azure Monitor limits

#### <a name="alerts"></a>경고

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>작업 그룹

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Log queries and language

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Log Analytics 작업 영역

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Notification Hubs limits
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Event Hubs 제한
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Service Bus 제한
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-central-limits"></a>IoT Central limits
[!INCLUDE [iot-central-limits](../includes/iot-central-limits.md)]

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

### <a name="event-grid-limits"></a>Event Grid limits
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Azure Maps 제한
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Azure Policy 제한
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>StorSimple 시스템 제한
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Backup 제한
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Azure SignalR Service limits
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>사이트 복구 제한
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>API Management 제한
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Azure Cache for Redis 제한
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>키 값 제한
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication limits
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Automation 한도
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Identity Manager limits
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>역할 기반 액세스 제어 한도
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>SQL Database 제한
For SQL Database limits, see [SQL Database resource limits for single databases](sql-database/sql-database-vcore-resource-limits-single-databases.md), [SQL Database resource limits for elastic pools and pooled databases](sql-database/sql-database-vcore-resource-limits-elastic-pools.md), and [SQL Database resource limits for managed instances](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>SQL Data Warehouse 제한
For SQL Data Warehouse limits, see [SQL Data Warehouse resource limits](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>참고 항목
- [Understand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
- [Virtual machine and cloud service sizes for Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Sizes for Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)
