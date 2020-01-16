---
title: Azure Migrate 정보
description: Azure Migrate 서비스에 대해 알아봅니다.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: c336b2f95a17896edcc7eaad2b2586c6859804f9
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772196"
---
# <a name="about-azure-migrate"></a>Azure Migrate 정보

이 문서에서는 Azure Migrate 서비스에 대한 간단한 개요를 제공합니다.

Azure Migrate를 사용하여 Azure로 마이그레이션합니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터를 평가하고 Azure로 마이그레이션할 수 있는 중앙 허브를 제공하며, 다음과 같은 기능을 갖추고 있습니다.

- **통합 마이그레이션 플랫폼**: Azure로의 마이그레이션 과정을 시작, 실행 및 추적하는 단일 포털입니다.
- **도구 범위**: 평가 및 마이그레이션에 사용할 도구 범위입니다. 허브에는 Azure Migrate: Server Assessment 및 Azure Migrate: Server Migration에 연결합니다. 다른 Azure 서비스, 기타 도구 및 ISV(독립 소프트웨어 공급업체) 제품과 통합됩니다.
- **평가 및 마이그레이션**: Azure Migrate 허브에서 다음을 평가하고 마이그레이션할 수 있습니다.
    - **서버**: 온-프레미스 서버를 평가하고 Azure VM으로 마이그레이션합니다.
    - **데이터베이스**: 온-프레미스 데이터베이스를 평가하고 Azure SQL DB 또는 Azure SQL Managed Instance로 마이그레이션합니다.
    - **웹 애플리케이션**: Azure App Service Assistant를 사용하여 온-프레미스 웹 애플리케이션을 평가하고 Azure App Service로 마이그레이션합니다.
    - **가상 데스크톱**: 온-프레미스 VDI(가상 데스크톱 인프라)를 평가하고 Azure의 Windows Virtual Desktop으로 마이그레이션합니다.
    - **Data**: Azure Data Box 제품을 사용하여 대량의 데이터를 Azure로 빠르고 비용 효율적으로 마이그레이션합니다. 


## <a name="integrated-tools"></a>통합 도구

Azure Migrate 허브는 다음 도구를 제공합니다.

**도구** | **평가/마이그레이션** | **세부 정보**
--- | --- | ---
**Azure Migrate: Server Assessment** | 서버 평가 | 온-프레미스 VMware VM, Hyper-V VM 및 물리적 서버를 검색하고 평가하여 Azure로 마이그레이션할 준비가 되었는지 여부를 확인합니다.
**Azure Migrate: Server Migration** | 서버 마이그레이션 | VMware VM, Hyper-V VM, 물리적 서버, 기타 가상화된 머신 및 퍼블릭 클라우드 VM을 Azure로 마이그레이션합니다. 
**DMA(Database Migration Assistant)** | Azure SQL DB, Azure SQL Managed Instance 또는 SQL Server를 실행하는 Azure VM으로 마이그레이션하기 위해 온-프레미스 SQL Server 데이터베이스를 평가합니다. | DMA는 마이그레이션의 잠재적인 차단 문제에 대한 정보를 제공합니다. 지원되지 않는 기능과 마이그레이션 후에 활용할 수 있는 새로운 기능을 식별하고, 데이터베이스 마이그레이션의 올바른 경로를 식별할 수 있도록 합니다. [자세히 알아보기](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**DMS(Database Migration Service)** | 온-프레미스 데이터베이스를 SQL, Azure SQL DB 및 Azure SQL Managed Instance를 실행하는 Azure VM으로 마이그레이션합니다. | [DMS](https://docs.microsoft.com/azure/dms/dms-overview)에 대해 자세히 알아보세요.
**Movere** | 서버 평가 | Movere에 대해 [자세히 알아보세요](#movere).
**Web App Migration Assistant** | 온-프레미스 웹앱을 평가하고 Azure로 마이그레이션합니다. |  Azure App Service Migration Assistant를 사용하여 Azure App Service으로 마이그레이션하기 위한 온-프레미스 웹 사이트를 평가합니다.<br/><br/> Azure App Service Migration Assistant를 사용하여 .NET 및 PHP 웹앱을 Azure로 마이그레이션합니다. Assistant에 대해 [자세히 알아보세요](https://appmigration.microsoft.com/).
**Azure Data Box** | 오프라인 데이터 마이그레이션. | Azure Data Box 제품을 사용하여 대량의 데이터를 오프라인으로 Azure로 이동합니다. [자세히 알아보기](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>ISV 통합

Azure Migrate는 다양한 ISV 제품과 통합됩니다. 

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


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: Server Assessment 도구

Azure Migrate: 서버 평가 도구는 Azure로 마이그레이션에 대해 온-프레미스 VMware VM, Hyper-V VM 및 물리적 서버를 검색 및 평가합니다. 이 도구는 다음을 식별하는 데 도움이 됩니다.

- **Azure 준비 상태:** 온-프레미스 컴퓨터가 Azure로 마이그레이션할 준비가 되었는지 여부를 평가합니다.
- **Azure 크기 조정:** 마이그레이션 후의 Azure VM의 예측된 크기입니다.
- **Azure 비용 예측:** Azure에서 온-프레미스 서버를 실행하기 위한 비용을 예측했습니다.
- **종속성 시각화:** 종속성 시각화에 서버 평가를 사용하는 경우 서버 간 종속성 및 종속 서버를 Azure로 이동하는 최적의 방법을 효과적으로 찾을 수 있습니다.

서버 평가는 온-프레미스에 배포하고 서버 평가에 등록하는 경량 어플라이언스를 사용합니다.

- 어플라이언스는 물리적 서버 또는 VM에서 실행되며 다운로드한 템플릿을 사용하여 쉽게 설치할 수 있습니다.
- 어플라이언스는 온-프레미스 머신을 검색하고, 머신 메타데이터 및 성능 데이터를 Azure Migrate에 지속적으로 보냅니다.
- 어플라이언스 검색은 에이전트 없이 수행됩니다. 검색된 컴퓨터에는 아무 것도 설치할 필요가 없습니다.
- 검색 후에는 검색된 머신을 그룹으로 수집하고, 마이그레이션할 그룹을 평가합니다.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: 서버 마이그레이션 도구

Azure Migrate: 서버 마이그레이션 도구를 사용하면 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버, 기타 가상화된 머신 및 퍼블릭 클라우드 VM을 Azure로 손쉽게 마이그레이션할 수 있습니다. 머신을 평가 후 또는 평가 없이 마이그레이션할 수 있습니다.


## <a name="select-a-tool"></a>도구 선택

Azure Migrate 허브에서 평가에 사용할 도구를 선택하고 Azure Migrate 프로젝트에 추가합니다. ISV 도구 또는 Movere를 추가하는 경우 다음을 수행합니다.

- 도구 지침에 따라 라이선스를 얻거나 평가판에 가입하여 시작합니다. 도구 라이선스는 ISV/도구에 따라 결정됩니다. 
- 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 지침에 따라 연결합니다.
- 모든 도구를 통해 Azure Migrate 프로젝트 내부에서 마이그레이션 과정을 추적합니다.


## <a name="movere"></a>Movere

Movere는 하루 만에 전체 IT 환경을 정확하게 제시하여 비즈니스 인텔리전스를 향상시키는 SaaS 플랫폼입니다. 조직이 성장, 변화 및 디지털 방식으로 최적화함에 따라 이 솔루션은 플랫폼, 애플리케이션 또는 지역에 관계없이 자체의 환경에 대한 가시성과 제어를 갖추는 데 필요한 자신감을 기업에 제공합니다. Movere는 Microsoft에서 [인수](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)했으며 더 이상 독립 실행형 제품으로 판매되지 않습니다.  Movere는 Microsoft 솔루션 평가 및 클라우드 경제 프로그램을 통해 사용할 수 있습니다. Movere에 대해 [자세히 알아보세요](https://www.movere.io). 질문이 있는 경우 movereq@microsoft.com에 제출하거나 Microsoft 담당자에게 문의하세요.

기본 제공 마이그레이션 서비스인 Azure Migrate도 살펴보는 것이 좋습니다. Azure Migrate는 클라우드로의 마이그레이션을 간소화하는 중앙 허브를 제공합니다. 허브는 물리적 서버와 가상 서버, 데이터베이스 및 애플리케이션을 포함하여 다양한 워크로드를 포괄적으로 지원합니다. 엔드투엔드 가시성을 통해 검색, 평가 및 마이그레이션 전체에서 진행 상황을 쉽게 추적할 수 있습니다. Azure 및 파트너 ISV 도구가 모두 기본적으로 제공되는 Azure Migrate에는 가상 서버/물리적 서버 검색, 올바른 성능 기반 크기 조정, 비용 계획, 가져오기 기반 평가 및 에이전트리스 애플리케이션 종속성 분석을 포함한 다양한 기능이 있습니다. 시작하는 데 전문가의 도움이 필요한 경우 Microsoft는 숙련된 [Azure Expert 관리형 서비스 공급자](https://azure.microsoft.com/partners)를 사용하여 진행 과정을 안내합니다. [Azure Migrate 웹 사이트](https://azure.microsoft.com/services/azure-migrate/)를 확인하세요. 
 

## <a name="azure-migrate-versions"></a>Azure Migrate 버전

Azure Migrate 서비스에는 다음과 같은 두 가지 버전이 있습니다.

- **현재 버전**: 이 버전을 사용하여 Azure Migrate 프로젝트를 만들고, 온-프레미스 컴퓨터를 검색하고, 평가 및 마이그레이션을 오케스트레이션합니다. 이 버전의 새로운 기능에 대해 [자세히 알아보세요](whats-new.md).
- **이전 버전**: 이전 버전의 Azure Migrate를 사용했으면(온-프레미스 VMware VM의 평가만 지원됨) 이제 현재 버전을 사용해야 합니다. 이전 버전을 사용해서는 Azure Migrate 프로젝트를 더 이상 만들 수 없으며 새 검색을 수행하지 않는 것이 좋습니다. 기존 프로젝트에 액세스하려면 Azure Portal에서 **Azure Migrate**를 검색하고 선택합니다. **Azure Migrate** 대시보드에는 이전 Azure Migrate 프로젝트에 액세스할 수 있는 알림 및 링크가 있습니다.



## <a name="next-steps"></a>다음 단계

- [VMware VM](tutorial-prepare-vmware.md), [Hyper-V VM](tutorial-prepare-hyper-v.md) 및 [물리적 서버](tutorial-prepare-physical.md)를 평가하는 자습서를 진행해 보세요.
- Azure Migrate에 대한 [질문과 대답을 검토합니다](resources-faq.md).
