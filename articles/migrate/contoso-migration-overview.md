---
title: Contoso 마이그레이션 시리즈 | Microsoft Docs
description: Contoso에서 온-프레미스 데이터 센터를 Azure로 마이그레이션하는 데 사용되는 마이그레이션 전략과 시나리오에 대한 개요를 제공합니다.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: raynew
ms.openlocfilehash: 048772edadca36a63870a2965c703ca7e6ec8c63
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729897"
---
# <a name="contoso-migration-series"></a>Contoso 마이그레이션 시리즈


일련의 어떻게 Contoso 마이그레이션합니다 가상의 조직을 온-프레미스 인프라를 보여 주는 문서를 있다고 합니다 [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 클라우드입니다. 

이 시리즈에는 정보 및 마이그레이션 인프라를 설정하고, 다양한 유형의 마이그레이션을 실행하는 방법을 보여 주는 시나리오가 포함되어 있습니다. 시나리오 진행 되는 상황 처럼 복잡성이 증가 합니다. 이러한 문서에서는 Contoso 회사에서 마이그레이션 임무를 수행하는 방법을 보여 주지만, 일반적인 참조를 위한 일반적인 참조를 위한 조언과 구체적인 지침이 도처에서 제공됩니다.

## <a name="migration-articles"></a>마이그레이션 문서

시리즈의 문서는 아래 표에 요약되어 있습니다.  

- 각 마이그레이션 시나리오는 마이그레이션 전략을 결정하는 약간씩 다른 비즈니스 목표를 기반으로 합니다.
- 배포 시나리오마다 비즈니스 영향 요소와 목표, 제안된 아키텍처, 마이그레이션을 수행하는 단계, 마이그레이션이 완료된 후의 정리 및 다음 단계에 대한 권장 사항 정보를 제공합니다.

**문서** | **세부 정보** 
--- | --- 
[문서 1: 개요](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | 문서 시리즈, Contoso의 마이그레이션 전략 및 시리즈에서 사용되는 샘플 앱에 대해 간략히 설명합니다. 
[문서 2: Azure 인프라 배포](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso에서 마이그레이션을 위해 온-프레미스 인프라와 Azure 인프라를 준비합니다. 이 시리즈의 모든 마이그레이션 관련 문서에서 동일한 인프라가 사용됩니다. 
[문서 3: Azure로 마이그레이션하기 위한 온-프레미스 리소스 평가](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-assessment)  | Contoso가 VMware에서 실행되는 온-프레미스 SmartHotel360 앱의 평가를 실행합니다. Contoso에서 Azure Migrate 서비스를 사용하여 앱 VM을 평가하고, Database Migration Assistant를 사용하여 앱 SQL Server 데이터베이스를 평가합니다.
[문서 4: Azure VM 및 SQL Database Managed Instance에서 앱 다시 호스트](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso가 온-프레미스 SmartHotel360 앱을 Azure로 리프트 앤 시프트 방식으로 마이그레이션합니다. Contoso에서 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다.
[문서 5: Azure VM에서 앱 다시 호스트](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso에서 Site Recovery 서비스를 사용하여 SmartHotel360 앱 VM을 Azure VM으로 마이그레이션합니다. 
[문서 6: Azure VM 및 SQL Server AlwaysOn 가용성 그룹에서 앱 다시 호스트](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso가 SmartHotel360 앱을 마이그레이션합니다. Contoso에서 Site Recovery를 사용하여 앱 VM을 마이그레이션하고, Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹으로 보호되는 SQL Server 클러스터로 마이그레이션합니다. 
[문서 7: Azure VM에서 Linux 앱 다시 호스트](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso에서 Site Recovery 서비스를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 방식으로 마이그레이션합니다.
[문서 8: Azure VM 및 Azure Database for MySQL에서 Linux 앱 다시 호스트](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso에서 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고, MySQL Workbench를 사용하여 앱 데이터베이스를 Azure Database for MySQL로 마이그레이션합니다. 
[문서 9: Azure 웹앱 및 Azure SQL Database에서 앱 리팩터링](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso에서 SmartHotel360 앱을 Azure Web App으로 마이그레이션하고, Database Migration Assistant를 사용하여 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션합니다.     
[문서 10: Azure 웹앱 및 Azure Database for MySQL에서 Linux 앱 리팩터링](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso에서 지속적인 업데이트를 위해 GitHub와 통합된 Azure Traffic Manager를 사용하여 Linux osTicket 앱을 여러 Azure 지역의 Azure 웹앱으로 마이그레이션합니다. Contoso에서 앱 데이터베이스를 Azure Database for MySQL 인스턴스로 마이그레이션합니다. 
[문서 11: Azure DevOps Services에서 Team Foundation Server 리팩터링](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso에서 온-프레미스 Team Foundation Server 배포를 Azure의 Azure DevOps Services로 마이그레이션합니다.
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rearchitect-container-sql) | Contoso에서 SmartHotel 앱을 Azure로 마이그레이션합니다. 그런 다음, 웹앱 계층을 Azure Service Fabric에서 실행되는 Windows 컨테이너로 재설계하고 Azure SQL Database를 사용하여 데이터베이스를 재설계합니다. 
[문서 13: Azure에서 앱 다시 빌드](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso에서 다양한 Azure 기능과 서비스(Azure App Service, AKS(Azure Kubernetes Service), Azure Functions, Azure Cognitive Services 및 Azure Cosmos DB 포함)를 사용하여 SmartHotel 앱을 다시 빌드합니다.  
[문서 14: Azure로의 마이그레이션 확장](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | 마이그레이션 조합을 시도한 후 Contoso는 Azure로 전체 마이그레이션을 확장할 준비를 합니다. 


    

## <a name="next-steps"></a>다음 단계

[에 대 한 자세한](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) 클라우드 마이그레이션입니다. 

