---
title: Azure Migrate 정보 | Microsoft Azure
description: Azure Migrate 서비스의 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8b1e716e482c49ceba0c600aaba30fe276bdb74d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748012"
---
# <a name="about-azure-migrate"></a>Azure Migrate 정보

이 문서에서는 Azure Migrate에 대한 빠른 개요를 제공합니다.

Azure Migrate를 사용하면 엔터프라이즈를 온-프레미스에서 Azure로 마이그레이션할 수 있습니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다.  Azure Migrate 제공:

- **통합 마이그레이션 플랫폼**: Azure로의 마이그레이션 과정을 시작, 실행 및 추적하는 단일 포털입니다.
- **도구 범위**: 허브에서 Azure Migrate 도구 평가 및 마이그레이션을 제공하며, 다른 Azure 서비스 외에도 기타 도구와 ISV(독립 소프트웨어 공급업체) 제품과 통합됩니다.
- **워크로드**: Azure Migrate에서 제공하는 평가 및 마이그레이션의 대상은 다음과 같습니다.
    - **서버**: Azure Migrate 서버 평가, Azure Migrate 서버 마이그레이션 및 기타 도구를 사용하여 서버를 평가하고 Azure VM으로 마이그레이션합니다.
    - **데이터베이스**: Microsoft 및 ISV 도구를 활용하여 온-프레미스 데이터베이스를 평가하고 Azure SQL DB 또는 Azure SQL Managed Instance로 마이그레이션합니다.
    - **웹 애플리케이션**: Azure App Service Assistant를 사용하여 온-프레미스 웹 애플리케이션을 평가하고 Azure App Service로 마이그레이션합니다.
    - **가상 데스크톱**: ISV 도구를 사용하여 온-프레미스 VDI(가상 데스크톱 인프라)를 평가하고 Azure의 Windows Virtual Desktop으로 마이그레이션합니다.
    - **데이터**: Azure Data Box 제품군을 사용하여 대량의 데이터를 Azure로 빠르고 비용 효율적으로 마이그레이션합니다.

## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스에는 현재 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**: 이 버전을 사용하여 Azure Migrate 프로젝트를 만들고, 온-프레미스 컴퓨터를 검색하고, 평가 및 마이그레이션을 오케스트레이션합니다. 이 버전의 새로운 기능에 대해 [자세히 알아보세요](whats-new.md).
- **이전 버전**: 이전 버전의 Azure Migrate를 사용했으면(온-프레미스 VMware VM의 평가만 지원됨) 이제 현재 버전을 사용해야 합니다. 이전 버전을 사용해서는 Azure Migrate 프로젝트를 더 이상 만들 수 없으며 새 검색을 수행하지 않는 것이 좋습니다. 기존 프로젝트에 액세스하려면 Azure Portal > **모든 서비스**에서 **Azure Migrate**를 검색합니다. Azure Migrate 대시보드에는 이전 Azure Migrate 프로젝트에 액세스할 수 있는 알림 및 링크가 있습니다.



## <a name="isv-integration"></a>ISV 통합

Azure Migrate는 네이티브 Azure 도구 외에도, 다양한 ISV 제품에 연결됩니다. 

**ISV** | **기능**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 서버 마이그레이션
[Cloudamize](https://www.cloudamize.com/platform) | 서버 평가
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 서버 평가 및 마이그레이션
[Device 42](https://docs.device42.com/) | 서버 평가
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI 평가
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 서버 마이그레이션
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 서버 평가
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 서버 및 데이터베이스 평가

## <a name="azure-tool-integration"></a>Azure 도구 통합

다음 표에는 Azure Migrate에 통합된 다른 도구가 요약되어 있습니다.

**도구** | **세부 정보**
--- | ---
Azure Migrate: Server Assessment | 서버 평가
Azure Migrate: 준비 | 서버 마이그레이션
DMA(Database Migration Assistant) | 데이터베이스 평가
DMS(Database Migration Service) | 데이터베이스 마이그레이션
Movere | 서버 평가
Web App Migration Assistant | 웹앱 평가 및 마이그레이션



### <a name="selecting-a-tool"></a>도구 선택

필요한 도구를 식별하고, 이를 Azure Migrate 프로젝트에 추가합니다.

- ISV 도구 또는 Movere를 추가하는 경우 다음을 수행합니다.
    - 도구 정책에 따라 라이선스를 얻거나 평가판에 가입하여 시작할 수 있습니다. 도구 라이선스는 ISV 또는 도구 라이선스 모델을 따릅니다.
    - 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구 지침 및 설명서에 따라 도구를 Azure Migrate에 연결합니다.
- Azure Migrate 프로젝트 내에서 Azure 및 기타 도구를 통해 마이그레이션 진행 상황을 중앙에서 추적합니다.



## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate 서버 평가 도구

Azure Migrate: 서버 평가 도구는 Azure로 마이그레이션에 대해 온-프레미스 VMware VM, Hyper-V VM 및 물리적 서버를 검색 및 평가합니다. 이 도구는 다음을 식별하는 데 도움이 됩니다.

- **Azure 준비 상태:** 온-프레미스 컴퓨터가 Azure로 마이그레이션할 준비가 되었는지 여부를 평가합니다.
- **Azure 크기 조정:** 마이그레이션 후의 Azure VM의 예측된 크기입니다.
- **Azure 비용 예측:** Azure에서 온-프레미스 서버를 실행하기 위한 비용을 예측했습니다.
- **종속성 시각화:** 서버 간 종속성(종속성 시각화가 사용되는 경우) 및 종속 서버를 Azure로 이동하는 최적의 방법입니다.

서버 평가는 온-프레미스에 배포하고 서버 평가에 등록하는 경량 어플라이언스를 사용합니다.

- 어플라이언스는 온-프레미스 컴퓨터를 검색합니다.
- 서버 평가에 연결하고 지속적으로 컴퓨터 메타데이터 및 성능 데이터를 Azure Migrate로 보냅니다.
- 어플라이언스 검색은 에이전트 없이 수행됩니다. 검색된 컴퓨터에는 아무 것도 설치할 필요가 없습니다.
- 검색 후에는 검색된 컴퓨터를 그룹으로 수집합니다. 일반적으로 함께 마이그레이션하려는 컴퓨터를 수집합니다.
- 그룹에 대한 평가를 만듭니다. 그런 다음, 평가를 분석하여 마이그레이션 전략을 파악합니다.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate 서버 마이그레이션 도구

Azure Migrate: 서버 마이그레이션 도구를 사용하면 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버, 기타 가상화된 머신 및 퍼블릭 클라우드 VM을 Azure로 손쉽게 마이그레이션할 수 있습니다. 평가 후에 또는 평가 없이 머신을 마이그레이션할 수 있습니다.


## <a name="database-migration-assistant"></a>Data Migration Assistant

Azure Migrate는 Microsoft DMA(Data Migration Assistant)에 연결하여 Azure SQL DB, Azure SQL Managed Instance 또는 SQL Server를 실행하는 Azure VM으로의 마이그레이션을 위해 온-프레미스 SQL Server 데이터베이스를 평가합니다. DMA는 마이그레이션의 잠재적인 차단 문제에 대한 정보를 제공합니다. 지원되지 않는 기능과 마이그레이션 후에 활용할 수 있는 새로운 기능을 식별하고, 데이터베이스 마이그레이션의 올바른 경로를 식별할 수 있도록 합니다. [자세히 알아보기](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Database Migration Service

Azure Migrate는 DMS(Azure Database Migration Service)와 연결하여 온-프레미스 데이터베이스를 Azure로 마이그레이션합니다. DMS를 사용하여 온-프레미스 데이터베이스를 SQL, Azure SQL DB 및 Azure SQL Managed Instance를 실행하는 Azure VM으로 마이그레이션합니다. [자세히 알아보기](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
Movere는 하루 만에 전체 IT 환경을 정확하게 제시하여 비즈니스 인텔리전스를 향상시키는 SaaS 플랫폼입니다. 조직이 성장, 변화 및 디지털 방식으로 최적화함에 따라 이 솔루션은 플랫폼, 애플리케이션 또는 지역에 관계없이 자체의 환경에 대한 가시성과 제어를 갖추는 데 필요한 자신감을 기업에 제공합니다. Movere는 Microsoft에서 [인수](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)했으며 더 이상 독립 실행형 제품으로 판매되지 않습니다.  Movere는 Microsoft 솔루션 평가 및 클라우드 경제 프로그램을 통해 사용할 수 있습니다. Movere에 대해 [자세히 알아보세요](https://www.movere.io). 질문이 있는 경우 movereq@microsoft.com에 제출하거나 Microsoft 담당자에게 문의하세요.

기본 제공 마이그레이션 서비스인 Azure Migrate도 살펴보는 것이 좋습니다. Azure Migrate는 클라우드로의 마이그레이션을 간소화하는 중앙 허브를 제공합니다. 허브는 물리적 서버와 가상 서버, 데이터베이스 및 애플리케이션을 포함하여 다양한 워크로드를 포괄적으로 지원합니다. 엔드투엔드 가시성을 통해 검색, 평가 및 마이그레이션 전체에서 진행 상황을 쉽게 추적할 수 있습니다. Azure 및 파트너 ISV 도구가 모두 기본적으로 제공되는 Azure Migrate에는 가상 서버/물리적 서버 검색, 올바른 성능 기반 크기 조정, 비용 계획, 가져오기 기반 평가 및 에이전트리스 애플리케이션 종속성 분석을 포함한 다양한 기능이 있습니다. 시작하는 데 전문가의 도움이 필요한 경우 Microsoft는 숙련된 [Azure Expert 관리형 서비스 공급자](https://azure.microsoft.com/partners)를 사용하여 진행 과정을 안내합니다. [Azure Migrate 웹 사이트](https://azure.microsoft.com/services/azure-migrate/)를 확인하세요. 
 

## <a name="web-app-migration-assistant"></a>Web App Migration Assistant

Azure Migrate는 Azure App Service Migration Assistant와 통합됩니다. Azure Migrate 허브에서 Assistant를 사용하여 다음과 같이 온-프레미스 웹앱을 평가하고 Azure로 마이그레이션할 수 있습니다.

- **온라인으로 웹앱 평가**: Azure App Service Migration Assistant를 사용하여 Azure App Service으로 마이그레이션하기 위한 온-프레미스 웹 사이트를 평가합니다.
- **웹앱 마이그레이션**: Azure App Service Migration Assistant를 사용하여 .NET 및 PHP 웹앱을 Azure로 마이그레이션합니다.

Assistant에 대해 [자세히 알아보세요](https://appmigration.microsoft.com/).



## <a name="offline-data-migration"></a>오프라인 데이터 마이그레이션

Azure Data Box 제품을 사용하여 대량의 데이터를 오프라인으로 Azure로 이동할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>다음 단계

- [VMware VM](tutorial-assess-vmware.md) 및 [Hyper-V VM](tutorial-assess-hyper-v.md)을 평가하는 자습서를 사용해 보세요.
- Azure Migrate 가격 책정에 대해 [자세히 알아봅니다](https://azure.microsoft.com/pricing/details/azure-migrate/).
- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).
