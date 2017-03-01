---
title: "사용 가능한 Azure Government 서비스 | Microsoft Docs"
description: "Azure Government에 제공되는 서비스의 개요 설명"
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: ffe3991f68bbd255ff6f4ffedbd6638f32897d1a
ms.openlocfilehash: 914870f59b488716bb283162078535849f732018
ms.lasthandoff: 02/21/2017


---
# <a name="available-services-on-azure-government"></a>Azure Government에서 사용 가능한 서비스
Azure Government는 제공되는 서비스를 지속적으로 확장하고 있습니다.  이러한 서비스는 Azure Public에 사용되는 것과 동일한 코드를 사용하여 배포됩니다.  이 섹션에서는 두 가지 유형의 키 정보를 포함하여 Azure Government에서 현재 사용 가능한 서비스를 설명합니다.

* **변형:** 아직 배포되지 않은 기능 또는 정부 환경의 고유한 속성(예: URL)으로 인한 변형입니다.  
* **고려 사항:** 데이터가 규정 준수 경계 내에 머물게 하기 위한 정부 관련 구현 세부 정보입니다.

이러한 서비스에 대해 알아야 하는 그 외의 모든 사항은 일반 문서에서 찾을 수 있습니다.

서비스의 최신 목록에 대해서는 [지역별 제품](https://azure.microsoft.com/regions/services/)을 참조하세요. 

아래 테이블에서 Resource Manager로 지정된 서비스를 사용하면 리소스 공급자를 사용할 수 있고 PowerShell을 사용하여 관리할 수 있습니다. Resource Manager 공급자, API 버전 및 스키마에 대한 자세한 내용은 [여기](../azure-resource-manager/resource-manager-supported-services.md)를 참조하세요. 포털에서 사용 가능하다고 지정된 서비스는 [Azure Government 포털](https://portal.azure.us/)에서 관리될 수 있습니다. 


## <a name="computedocumentation-government-computemd"></a>[계산](documentation-government-compute.md)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| [가상 컴퓨터](documentation-government-compute.md#virtual-machines) | 예 | 예 |
| 배치 | 예 | 예 |
| 클라우드 서비스 | 예 | 예 |
| Service Fabric | 예 | 예 |
| VM 크기 집합 | 예 | 예 |


## <a name="networkingdocumentation-government-networkingmd"></a>[네트워킹](documentation-government-networking.md)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | 예 | 예 |
| 가상 네트워크 | 예 | 예 |
| [부하 분산 장치](documentation-government-networking.md#support-for-load-balancer) | 예 | 예 |
| [Traffic Manager](documentation-government-networking.md#support-for-traffic-manger) | 예 | 예 |
| [VPN Gateway](documentation-government-networking.md#support-for-vpn-gateway) | 예 | 예 |
| 응용 프로그램 게이트웨이 | 예 | 예 |
| ExpressRoute | 예 | 예 |



## <a name="storagedocumentation-government-services-storagemd"></a>[저장소](documentation-government-services-storage.md)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| [저장소 - Blob](documentation-government-services-storage.md#azure-storage) | 예 | 예 |
| [저장소 - 테이블](documentation-government-services-storage.md#azure-storage) | 예 | 예 |
| [저장소 - 큐](documentation-government-services-storage.md#azure-storage) | 예 | 예 |
| [저장소 - 파일](documentation-government-services-storage.md#azure-storage) | 예 | 예 |
| [저장소 - 디스크](documentation-government-services-storage.md#azure-storage) | 예 | 예 |
| [StorSimple](documentation-government-services-storage.md) | 예 | 예 |
| [백업](documentation-government-services-storage.md#azure-storage) | 예 | 예 |
| [사이트 복구](documentation-government-services-storage.md#azure-storage) | 예 | 예 |
| [가져오기/내보내기](documentation-government-services-storage.md#azure-storage) | 예 | 아니요 |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[웹 + 모바일](documentation-government-services-webandmobile.md)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| [App Service - Web Apps](documentation-government-services-webandmobile.md#app-services) | 예 | 예 |
| [App Service - API Apps](documentation-government-services-webandmobile.md#app-services) | 예 | 예 |
| [App Service - Mobile Apps](documentation-government-services-webandmobile.md#app-services) | 예 | 예 |
| 미디어 서비스 | 예 | 예 |


## <a name="databasesdocumentation-government-services-databasemd"></a>[데이터베이스](documentation-government-services-database.md)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| [SQL 데이터베이스](documentation-government-services-database.md#sql-database) | 예 | 예 |
| SQL Data Warehouse | 예 | 예 |
| SQL Server 스트레치 데이터베이스 | 예 | 예 |
| [Redis Cache](documentation-government-services-database.md#azure-redis-cache) | 예 | 예 |




## <a name="internet-of-things-iot"></a>IoT(사물 인터넷)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| 이벤트 허브(영문) | 예 | 예 |
| 알림 허브 | 아니요 | 아니요([레거시 포털](https://manage.windowsazure.us/)로 이동) |


## <a name="enterprise-integration"></a>엔터프라이즈 통합

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| 서비스 버스 | 예 | 예 |
| [StorSimple](documentation-government-services-storage.md) | 예 | 예 |
| SQL Server 스트레치 데이터베이스 | 예 | 예 |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[보안 + ID](documentation-government-services-securityandidentity.md)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| Azure Active Directory | 예 | 예 |
| [키 자격 증명 모음](documentation-government-services-securityandidentity.md#key-vault) | 예 | 아니요(포함 예정) |
| Multi-Factory Authentication | 예 | 예 |
| InTune | 예 | 아니요 |


## <a name="intelligence--analytics"></a>인텔리전스 + 분석

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| Power BI | 예 | 아니요 |
| HDInsight | 예 | 예 |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[모니터링 + 관리](documentation-government-services-monitoringandmanagement.md)

| 부여 | 리소스 관리자 사용 | 포털 |
| --- | --- | --- |
| [자동화](documentation-government-services-monitoringandmanagement.md#automation) | 예 | 예 |
| [백업](documentation-government-services-backup.md) | 예 | 예 |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | 예 | 예 |
| [사이트 복구](documentation-government-services-monitoringandmanagement.md#site-recovery) | 예 | 예 |
| 스케줄러 | 예 | 아니요 |
| 모니터링 및 진단 | 예 | 예 |




## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 [Microsoft Azure Government 블로그](https://blogs.msdn.microsoft.com/azuregov/)를 구독하세요.


