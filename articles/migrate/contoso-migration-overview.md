---
title: Contoso migration 시리즈 | Microsoft Docs
description: Azure로 마이그레이션하기 위한 Contoso 예제 마이그레이션 시나리오에 대 한 링크입니다.
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: raynew
ms.openlocfilehash: f9f7b3b64cf91019ed4e40d5d52b859419b74836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86107634"
---
# <a name="contoso-migration-series"></a>Contoso 마이그레이션 시리즈


가상 조직 Contoso가 온-프레미스 인프라를 [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 클라우드로 마이그레이션하는 방법을 보여 주는 일련의 문서가 있습니다. 

이 시리즈에는 인프라 마이그레이션을 설정 하는 방법 및 다양 한 유형의 마이그레이션을 실행 하는 방법을 설명 하는 시나리오가 포함 되어 있습니다. 시나리오는 진행 됨에 따라 복잡성이 증가 합니다. 이 문서에서는 Contoso 회사에서 마이그레이션을 처리 하는 방법을 보여 주지만 일반적인 지침 및 포인터는 전체에서 제공 됩니다.

## <a name="migration-articles"></a>마이그레이션 문서

시리즈의 문서는 아래 표에 요약되어 있습니다.  

- 각 마이그레이션 시나리오는 마이그레이션 전략을 결정 하는 약간 다른 비즈니스 요구 사항에 따라 달라 집니다.
- 각 배포 시나리오에 대해 비즈니스 드라이버 및 목표, 제안 된 아키텍처, 마이그레이션을 수행 하는 단계, 마이그레이션 완료 후 정리 및 다음 단계에 대 한 권장 사항에 대 한 정보를 제공 합니다.


**아티클** | **세부 정보** 
--- | --- 
[문서 1: 개요](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 문서 시리즈, Contoso의 마이그레이션 전략 및 시리즈에서 사용되는 샘플 앱에 대해 간략히 설명합니다. 
[문서 2: Azure 인프라 배포](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso는 마이그레이션에 대 한 온-프레미스 인프라와 Azure 인프라를 준비 합니다. 이 시리즈의 모든 문서에서 동일한 인프라가 사용됩니다. 
[문서 3: Azure로 마이그레이션할 온-프레미스 리소스 평가](/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso가 VMware에서 실행되는 온-프레미스 SmartHotel360 앱의 평가를 실행합니다. Contoso에서 Azure Migrate 서비스를 사용하여 앱 VM을 평가하고, Database Migration Assistant를 사용하여 앱 SQL Server 데이터베이스를 평가합니다.
[문서 4: Azure VM 및 SQL Managed Instance에서 앱 Rehost](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso가 온-프레미스 SmartHotel360 앱을 Azure로 리프트 앤 시프트 방식으로 마이그레이션합니다. Contoso는 [Azure Migrate](./migrate-services-overview.md)을 사용 하 여 앱 프런트 엔드 VM을 마이그레이션합니다. Contoso는 [Azure Database Migration Service](../dms/dms-overview.md)를 사용 하 여 앱 데이터베이스를 SQL Managed Instance 마이그레이션합니다.
[문서 5: 앱을 Azure VM에 다시 호스트](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso는 Azure Migrate 서비스를 사용 하 여 SmartHotel360 apps Vm을 Azure Vm으로 마이그레이션합니다. 
[문서 6: Azure VM 및 SQL Server AlwaysOn 가용성 그룹에서 앱 다시 호스트](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) | Contoso가 SmartHotel360 앱을 마이그레이션합니다. Contoso는 Azure Migrate을 사용 하 여 앱 Vm을 마이그레이션합니다. Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹으로 보호되는 SQL Server 클러스터로 마이그레이션합니다. 
[문서 7: Azure VM에서 Linux 앱 다시 호스트](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso는 Azure Migrate을 사용 하 여 Linux osTicket 앱의 리프트 앤 시프트 마이그레이션을 Azure Vm으로 완료 합니다.
[문서 8: Azure VM 및 Azure Database for MySQL에서 Linux 앱 다시 호스트](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso는 Azure Migrate을 사용 하 여 Linux osTicket 앱을 Azure Vm으로 마이그레이션합니다. Azure Database Migration Service를 사용 하 여 앱 데이터베이스를 MySQ 용 Azure Database로 마이그레이션합니다 (MySQL 워크 벤치를 사용 하는 대체 옵션 포함).
[문서 9: Azure 웹앱 및 Azure SQL Database에서 앱 리팩터링](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso는 SmartHotel360 앱을 Azure 웹 앱으로 마이그레이션하고 Azure Database Migration Service를 사용 하 여 앱 데이터베이스를 Azure SQL Database으로 마이그레이션합니다.
[문서 10: Azure 앱 서비스 및 SQL Managed Instance를 사용 하 여 Windows 앱 리팩터링](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql-managed-instance) | Contoso는 온-프레미스 Windows 기반 앱을 Azure 웹 앱으로 마이그레이션하고 Azure Database Migration Service를 사용 하 여 앱 데이터베이스를 Azure SQL Managed Instance로 마이그레이션합니다.
[문서 11: Azure 웹 앱에서 Linux 앱 리팩터링 및 Azure Database for MySQL](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso는 Azure Traffic Manager를 사용 하 여 여러 Azure 지역에서 azure 웹 앱으로 Linux osTicket 앱을 마이그레이션하고 지속적인 업데이트를 위해 GitHub와 통합 합니다. Contoso에서 앱 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션합니다. 
[문서 12: Azure DevOps Services에서 Team Foundation Server 리팩터링](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso에서 온-프레미스 Team Foundation Server 배포를 Azure의 Azure DevOps Services로 마이그레이션합니다.
[문서 13: Azure에서 앱 다시 빌드](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso는 Azure App Service, AKS (Azure Kubernetes Service), Azure Functions, Azure Cognitive Services 및 Azure Cosmos DB를 비롯 한 다양 한 Azure 기능 및 서비스를 사용 하 여 SmartHotel 앱을 다시 작성 합니다.
[문서 14: Azure로의 마이그레이션 확장](/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다.



## <a name="next-steps"></a>다음 단계

- 클라우드 마이그레이션 [에 대해 알아봅니다](/azure/architecture/cloud-adoption/migrate/) .
- [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 다른 시나리오 (원본/대상 쌍)에 대 한 마이그레이션 전략에 대해 알아봅니다.
