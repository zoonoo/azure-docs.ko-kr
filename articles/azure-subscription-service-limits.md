<properties
	pageTitle="Microsoft Azure 구독 및 서비스 제한, 할당량 및 제약 조건"
	description="일반적인 Azure 구독 및 서비스 제한, 할당량 및 제약 조건 목록을 제공합니다. 여기에는 최대값과 함께 제한을 늘리는 방법에 대한 정보가 포함됩니다."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="jroth"/>

# Azure 구독 및 서비스 제한, 할당량 및 제약 조건

## 개요

이 문서는 가장 일반적인 Microsoft Azure 제한을 지정합니다. 현재 이 문서에서는 일부 Azure 서비스에 대해 다룹니다. 시간 경과에 따라 이 제한은 더 많은 플랫폼에 적용되도록 확장 및 업데이트됩니다.

> [AZURE.NOTE] **기본 제한** 이상으로 제한을 높이려는 경우 [무료로 온라인 고객 지원 요청을 개설](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)할 수 있습니다. 다음 표에 나오는 **최대 제한** 값 이상으로 제한을 높일 수 없습니다. **최대 제한** 열이 없는 경우는 지정된 리소스에 조정 가능한 제한이 없습니다.

## 제한 및 Azure 리소스 관리자

이제 단일 Azure 리소스 그룹에 여러 Azure 리소스를 결합할 수 있습니다. 리소스 그룹을 사용하는 경우 전역이었던 제한이 Azure 리소스 관리자에서 지역 수준으로 관리됩니다. Azure 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](azure-portal/resource-group-portal.md)를 참조하세요.

아래 제한에서는 Azure 리소스 관리자를 사용할 때 제한에 차이를 반영할 수 있도록 새로운 테이블이 추가되었습니다. 예를 들어, **구독 제한** 테이블 및 **구독 제한 - Azure 리소스 관리자** 테이블이 있습니다. 두 시나리오에 모두 제한이 적용되면 첫 번째 테이블에서만 표시됩니다. 별도로 지정하지 않으면 제한은 모든 지역에 걸쳐 전역으로 적용됩니다.

> [AZURE.NOTE] Azure 리소스 그룹의 리소스에 대한 할당량은 구독을 통해 지역별로 액세스할 수 있으며, 구독별로는 액세스할 수 없는데 서비스 관리 할당량이 구독별로 액세스되기 때문입니다. 코어 할당량을 한 예로 살펴보겠습니다. 코어를 지원하는 할당량 증가를 요청해야 하는 경우 어떤 지역에서 얼마나 많은 코어를 사용할 것인지 결정한 다음, 원하는 금액 및 지역에 대한 Azure 리소스 그룹 코어 할당량에 대해 특정 요청을 만들어야 합니다. 따라서 서유럽 지역에서 응용 프로그램을 실행하려면 30개의 코어를 사용해야 하는 경우, 확실하게 서유럽에서 30개의 코어를 요청해야 합니다. 하지만 다른 지역에는 코어 할당량 증가가 없고 서유럽만 30개의 코어 할당량이 있게 됩니다.
<!-- -->
따라서 어떤 한 지역에서 워크로드에 필요한 Azure 리소스 그룹 할당량을 결정하고 배포를 고려 중인 각 지역에서 해당 금액을 요청하는 것이 유용할 수 있습니다. 특정 지역의 현재 할당량 검색에 대한 자세한 내용은 [배포 문제 해결](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues)을 참조하세요.


## 서비스 특정 제한

- [Active Directory](#active-directory-limits)
- [API 관리](#api-management-limits)
- [앱 서비스](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Azure Redis 캐시(영문)](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [백업](#backup-limits)
- [배치](#batch-limits)
- [BizTalk 서비스](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [클라우드 서비스](#cloud-services-limits)
- [데이터 팩터리](#data-factory-limits)
- [데이터 레이크 분석](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [DocumentDB](#documentdb-limits)
- [IoT 허브](#iot-hub-limits)
- [키 자격 증명 모음](#key-vault-limits)
- [미디어 서비스](#media-services-limits)
- [모바일 고객 관리](#mobile-engagement-limits)
- [모바일 서비스](#mobile-services-limits)
- [Multi-Factor Authentication](#multi-factor-authentication)
- [네트워킹](#networking-limits)
- [알림 허브 서비스](#notification-hub-service-limits)
- [Operational Insights](#operational-insights-limits)
- [리소스 그룹](#resource-group-limits)
- [스케줄러](#scheduler-limits)
- [이를 통해 검색](#search-limits)
- [서비스 버스](#service-bus-limits)
- [사이트 복구](#site-recovery-limits)
- [SQL 데이터베이스](#sql-database-limits)
- [저장소](#storage-limits)
- [StorSimple 시스템](#storsimple-system-limits)
- [스트림 분석](#stream-analytics-limits)
- [구독](#subscription-limits)
- [트래픽 관리자](#traffic-manager-limits)
- [가상 컴퓨터](#virtual-machines-limits)


### 구독 제한
#### 구독 제한
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### 구독 제한 - Azure 리소스 관리자

Azure 리소스 관리자 및 Azure 리소스 그룹을 사용하는 경우 다음과 같은 제한이 적용됩니다. Azure 리소스 관리자에서 변경되지 않는 제한은 아래에 나열되지 않습니다. 이러한 제한에 대해서는 이전 테이블을 참조하세요.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### 리소스 그룹 제한

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### 가상 컴퓨터 제한
#### 가상 컴퓨터 제한
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### 가상 컴퓨터 제한 - Azure 리소스 관리자

Azure 리소스 관리자 및 Azure 리소스 그룹을 사용하는 경우 다음과 같은 제한이 적용됩니다. Azure 리소스 관리자에서 변경되지 않는 제한은 아래에 나열되지 않습니다. 이러한 제한에 대해서는 이전 테이블을 참조하세요.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### 네트워킹 제한

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### 네트워킹 제한
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### 트래픽 관리자 제한

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### DNS 제한

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### 저장소 제한

저장소 계정 제한에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](../articles/storage/storage-scalability-targets.md)를 참조하세요.

#### 저장소 서비스 제한

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### 가상 컴퓨터 디스크 제한

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [가상 컴퓨터 크기](../articles/virtual-machines/virtual-machines-size-specs.md)를 참조하세요.

**표준 저장소 계정**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**프리미엄 저장소 계정**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### 저장소 리소스 공급자 제한

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### 클라우드 서비스 제한

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### 앱 서비스 제한
다음 앱 서비스 제한에는 웹앱, 모바일 앱, API 앱 및 논리 앱에 대한 제한이 포함됩니다.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### 스케줄러 제한

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### 배치 제한

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###BizTalk 서비스 제한
다음 표에서는 Azure Biztalk 서비스에 대한 제한을 보여 줍니다.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### DocumentDB 제한

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### 모바일 참여 제한

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### 검색 제한

가격 책정 계층은 검색 서비스의 용량 및 제한을 결정합니다. 계층은 다음을 포함합니다.

- **무료** 다중 테넌트 서비스는 다른 Azure 구독자와 공유되며 평가 및 소규모 개발 프로젝트용으로 사용하기 위한 것입니다.
- **기본(미리 보기)**은 소규모의 프로덕션 워크로드를 위한 전용 컴퓨팅 리소스를 제공합니다. 이 계층은 현재 미리 보기 상태이며 할인 요금으로 제공됩니다.
- **표준(S1 및 S2)**은 프로덕션 워크로드용입니다. 대용량 버전(**S2**)은 요청 시 사용할 수 있습니다. azuresearch_contact@microsoft.com)로 메일을 보내세요.

[AZURE.INCLUDE [azure-search-limits-all](../includes/azure-search-limits-all.md)]

문서 크기, 키, 요청 및 응답 등의 다른 제한 사항에 대한 자세한 내용은 [Azure 검색의 서비스 제한 사항](search/search-limits-quotas-capacity.md)을 참조하세요.

### 미디어 서비스 제한

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### CDN 제한

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### 모바일 서비스 제한

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### 알림 허브 서비스 제한

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### 서비스 버스 제한

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### IoT Hub 제한

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### 데이터 팩터리 제한

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### Data Lake 분석 제한
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### 스트림 분석 제한

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Active Directory 제한

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Azure RemoteApp 제한

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### StorSimple 시스템 제한

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Operational Insights 제한

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### 백업 제한

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### 사이트 복구 제한

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Application Insights 제한

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### API 관리 제한

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Azure Redis 캐시 제한

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### 키 값 제한

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### SQL 데이터베이스 제한

SQL 데이터베이스 제한은 [SQL 데이터베이스 리소스 제한](sql-database/sql-database-resource-limits.md)을 참조하세요.

## 참고 항목

[Azure 제한 및 증가 이해](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](virtual-machines/virtual-machines-size-specs.md)

[클라우드 서비스 크기](cloud-services/cloud-services-sizes-specs.md)

<!---HONumber=AcomDC_0302_2016-->