---
title: Azure 컨테이너 및 Azure SQL Database에서 Contoso 앱 아키텍처 변경 | Microsoft Docs
description: Contoso가 Azure Windows 컨테이너 및 Azure SQL Database에서 앱 아키텍처를 변경하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: raynew
ms.openlocfilehash: 7146865270accb73981b09be6409180c4ef1440f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003200"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Contoso 마이그레이션: 온-프레미스 앱 아키텍처를 Azure 컨테이너 및 Azure SQL Database로 변경

이 문서에서는 Contoso가 Azure에서 SmartHotel 앱을 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. Contoso는 앱 프런트 엔드 VM을 Azure Windows 컨테이너로, 앱 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션합니다.

이 문서는 가상 회사 Contoso가 온-프레미스 리소스를 Microsoft Azure 클라우드로 마이그레이션하는 방법을 보여 주는 문서 시리즈 중 하나입니다. 이 문서 시리즈에는 배경 정보와 마이그레이션 인프라 설정, 마이그레이션을 위한 온-프레미스 리소스 평가, 다양한 유형의 마이그레이션 실행을 설명하는 시나리오가 담겨 있습니다. 시나리오가 점점 더 복잡해지므로 점차 문서를 추가할 예정입니다.

**문서** | **세부 정보** | **상태**
--- | --- | ---
[문서 1: 개요](contoso-migration-overview.md) | Contoso 마이그레이션 전략, 문서 시리즈 및 사용할 샘플 앱에 대해 간략히 설명합니다. | 사용 가능
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso가 마이그레이션을 위해 온-프레미스 및 Azure 인프라를 준비하는 방법을 설명합니다. 모든 마이그레이션 문서에 동일한 인프라가 사용됩니다. | 사용 가능
[문서 3: 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | Contoso가 VMware에서 실행되는 온-프레미스 2계층 SmartHotel 앱 평가를 실행하는 방법을 보여 줍니다. Contoso가 [Azure Migrate](migrate-overview.md) 서비스로 앱 VM을 평가하고 [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)로 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: 앱을 Azure VM 및 SQL 관리되는 인스턴스에 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 SmartHotel 앱에 대해 Azure로의 리프트 앤 시프트 마이그레이션을 실행하는 방법을 보여 줍니다. Contoso는 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고 [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)를 사용하여 앱 데이터베이스를 SQL 관리되는 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 5: 앱을 Azure VM에 다시 호스트](contoso-migration-rehost-vm.md) | Contoso가 Site Recovery만 사용하여 SmartHotel 앱 VM을 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 6: 앱을 Azure VM 및 SQL Server Always On 가용성 그룹에 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 SmartHotel 앱을 마이그레이션하는 방법을 보여 줍니다. Contoso가 Site Recovery를 사용하여 앱 VM을 마이그레이션하고 Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹에서 보호하는 SQL Server 클러스터로 마이그레이션합니다. | 사용 가능
[문서 7: Azure VM에 Linux 앱 다시 호스팅](contoso-migration-rehost-linux-vm.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 8: Azure VM 및 Azure MySQL 서버에 Linux 앱 다시 호스팅](contoso-migration-rehost-linux-vm-mysql.md) | Contoso가 Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고 MySQL Workbench를 사용하여 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 9: Azure 웹앱 및 Azure SQL 데이터베이스에 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso가 SmartHotel 앱을 Azure 웹앱으로 마이그레이션하고, 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션하는 방법 시연 | 사용 가능
[문서 10: Azure Web Apps 및 Azure MySQL에 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso가 지속적인 업데이트를 위해 GitHub와 통합된, 여러 사이트의 Azure Web Apps로 Linux osTicket 앱을 마이그레이션하는 방법을 보여줍니다. Azure MySQL 인스턴스로 앱 데이터베이스를 마이그레이션합니다. | 사용 가능
문서 11: Azure 컨테이너 및 Azure SQL Database에서 앱 아키텍처 변경 | Contoso가 SmartHotel 앱을 Azure로 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. 웹앱 계층을 Windows 컨테이너 및 Azure SQL Database의 앱 데이터베이스로 아키텍처를 변경합니다. | 이 문서의 내용:
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 아키텍처 변경](contoso-migration-rearchitect-container-sql.md) | Contoso가 SmartHotel 앱을 Azure로 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. 웹앱 계층을 Windows 컨테이너 및 Azure SQL Database의 앱 데이터베이스로 아키텍처를 변경합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso가 App Services, Azure Kubernetes, Azure Functions, Cognitive services 및 Cosmos DB를 포함한 다양한 Azure 기능 및 서비스를 사용하여 해당 SmartHotel 앱을 다시 빌드하는 방법을 보여줍니다. | 사용 가능

이 문서에서 Contoso는 2계층 Windows를 마이그레이션합니다. .NET SmartHotel 앱을 Azure로 마이그레이션합니다. 이 앱을 사용하려는 경우 오픈 소스로 제공되므로 [GitHub](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다.

## <a name="business-drivers"></a>비즈니스 영향 요소

IT 리더십 팀은 비즈니스 파트너와의 긴밀한 협력을 통해 다음과 같이 기업이 이러한 마이그레이션으로 달성하고자 하는 바를 잘 이해하고 있습니다.

- **비즈니스 성장 문제 해결**: Contoso는 성장 중이므로 온-프레미스 시스템과 인프라에 부담이 있습니다.
- **효율성 증가**: Contoso는 불필요한 프로시저를 제거하고 개발자와 사용자가 사용할 프로세스를 간소화해야 합니다.  이 회사는 IT를 빠른 상태로 유지하고 시간이나 비용을 낭비하지 않도록 하여 고객 요구 사항을 더 빠르게 제공해야 합니다.
- **민첩성 향상**: Contoso IT에서 비즈니스 요구 사항에 대해 더 빠르게 응답해야 합니다. 글로벌 경제에서 성공할 수 있으려면 시장의 변화에 더 빠르게 대응할 수 있어야 합니다.  사업상의 걸림돌이 되지 않아야 합니다.
- **크기 조정**: 비즈니스가 성공적으로 성장함에 따라 Contoso IT에서 동일한 속도로 크기를 조정할 수 있는 시스템을 제공해야 합니다.
- **비용**: Contoso는 라이선스 비용을 최소화하려고 합니다.

## <a name="migration-goals"></a>마이그레이션 목표

Contoso 클라우드 팀은 이 마이그레이션에 대한 목표를 확정했습니다. 다음과 같은 목표는 최상의 마이그레이션 방법을 결정하는 데 사용되었습니다.

**목표** | **세부 정보**
--- | --- 
**앱 요구 사항** | Azure의 앱은 미래에도 지금처럼 중요한 역할을 할 것입니다.<br/><br/> 앞으로도 현재 VMWare에서 수행하는 것과 동일한 성능을 갖추어야 합니다.<br/><br/> Contoso는 현재 앱이 실행되고 있는 Windows Server 2008 R2 지원을 중지하고, 앱에 투자하기를 원합니다.<br/><br/> SQL Server 2008 R2에서 관리에 대한 필요성을 최소화하는 최신 PaaS 데이터베이스 플랫폼으로 이동하려고 합니다.<br/><br/> Contoso는 가능한 경우 SQL Server 라이선싱 및 Software Assurance에 대한 투자를 활용하려고 합니다.<br/><br/> 앱 웹 계층을 강화할 수 있기를 원합니다.
**제한 사항** | 앱은 동일한 VM에서 실행되는 ASP.NET 앱 및 WCF 서비스로 구성됩니다. Contoso는 Azure App Service를 사용하여 두 개의 웹앱에서 분할하려고 합니다. 
**Azure 요구 사항** | Contoso는 앱을 Azure로 이동하고, 컨테이너에서 실행하여 앱 수명을 연장하고 싶어 합니다. Azure에서 처음부터 새로 앱을 구현하는 것을 원하지는 않습니다. 

## <a name="solution-design"></a>솔루션 디자인

해당 목표 및 요구 사항을 고정한 후 Contoso는 배포 솔루션을 디자인 및 검토하고, 마이그레이션에 사용할 Azure 서비스를 포함한 마이그레이션 프로세스를 식별합니다.

### <a name="current-app"></a>현재 앱

- SmartHotel 온-프레미스 앱은 두 개의 VM(WEBVM 및 SQLVM)에 계층화되어 있습니다.
- VM은 VMware ESXi 호스트 **contosohost1.contoso.com**(버전 6.5)에 있습니다.
- VMware 환경은 VM에서 실행되는 vCenter Server 6.5(**vcenter.contoso.com**)에서 관리합니다.
- Contoso는 온-프레미스 데이터 센터(contoso-datacenter)와 온-프레미스 도메인 컨트롤러(**contosodc1**)를 갖고 있습니다.
- 마이그레이션이 완료되면 Contoso 데이터 센터의 온-프레미스 VM은 서비스 해제됩니다.


### <a name="proposed-architecture"></a>제안된 아키텍처

- 앱의 데이터베이스 계층의 경우 Contoso는 [이 문서](https://docs.microsoft.com/azure/sql-database/sql-database-features)를 사용하여 Azure SQL Database를 SQL Server와 비교했습니다. 몇 가지 이유로 Azure SQL Database로 이동하기로 결정했습니다.
    - Azure SQL Database는 관리되는 관계형 데이터베이스 서비스입니다. 0에 가까운 관리를 사용하여 여러 서비스 수준에서 예측 가능한 성능을 제공합니다. 이점으로 가동 중지 시간 없는 동적 확장성, 기본 제공 지능형 최적화 및 글로벌 확장성 및 가용성을 포함합니다.
    - 간단한 DMA(Data Migration Assistant)를 활용하여 온-프레미스 데이터베이스를 Azure SQL로 평가 및 마이그레이션할 수 있습니다.
    - Software Assurance와 함께 사용하면 SQL Database에서 SQL Server에 대한 Azure 하이브리드 혜택을 사용하여 기존 라이선스를 할인된 가격에 교환할 수 있습니다. 최대 30%까지 비용 절감을 제공할 수 있습니다.
    - SQL Database는 상시 암호화, 동적 데이터 마스킹 및 행 수준 보안/위협 검색을 포함하는 다양한 보안 기능을 제공합니다.
- 앱 웹 계층의 경우 Visual Studio를 사용하여 Windows 컨테이너로 변환하기로 결정했습니다.
    - Azure Service Fabric을 사용하여 앱을 배포하고, ACR(Azure Container Registry)에서 Windows 컨테이너 이미지를 끌어올 것입니다.
    - 감정 분석을 포함하도록 앱을 확장하기 위한 프로토타입은 Service Fabric에 또 다른 서비스로 구현하여 Cosmos DB에 연결할 것입니다.  이렇게 하면 트윗에서 정보를 읽고, 앱에 표시하게 됩니다.

    ![시나리오 아키텍처](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>솔루션 검토
Contoso는 장점 및 단점 목록을 함께 조합하여 제안된 디자인을 평가합니다.

**고려 사항** | **세부 정보**
--- | ---
**장점** | Azure Service Fabric으로 마이그레이션하기 위해 SmartHotel 앱 코드를 변경해야 합니다. 하지만 Service Fabric SDK 도구를 사용하여 변경하면 간단합니다.<br/><br/> Service Fabric으로 이동하면 원래 코드 베이스에 대한 위험 없이 차후에 신속하게 응용 프로그램에 추가할 마이크로 서비스 개발을 시작할 수 있습니다.<br/><br/> Windows 컨테이너는 일반 컨테이너와 동일한 혜택을 제공합니다. 민첩성, 이식성 및 제어성이 향상됩니다.<br/><br/> Contoso는 SQL Server와 Windows Server에 대한 Azure 하이브리드 혜택을 사용하여 Software Assurance에 대한 투자를 활용할 수 있습니다.<br/><br/> 마이그레이션 후에는 Windows Server 2008 R2를 더 이상 지원할 필요가 없습니다. [자세히 알아보기](https://support.microsoft.com/lifecycle).<br/><br/> Contoso는 더 이상 단일 실패 지점이 되지 않도록 여러 인스턴스를 사용하여 앱의 웹 계층을 구성할 수 있습니다.<br/><br/> 더 이상 오래된 SQL Server 2008 R2에 의존할 필요가 없습니다.<br/><br/> SQL Database는 Contoso의 기술 요구 사항을 지원합니다. Contoso는는 Database Migration Assistant를 사용하여 온-프레미스 데이터베이스를 평가하고 호환된다는 것을 확인했습니다.<br/><br/> SQL Database에는 Contoso에서 설정할 필요가 없는 기본 제공 내결함성이 있습니다. 따라서 데이터 계층은 더 이상 단일 장애 조치(failover) 지점이 아닙니다.
**단점** | 컨테이너는 다른 마이그레이션 옵션보다 복잡합니다. 컨테이너에 대한 학습 곡선이 Contoso에는 문제가 될 수 있습니다.  학습 곡선에도 불구하고 여러 가치를 제공하는 새로운 복잡성이 추가됩니다.<br/><br/> Contoso의 운영 팀은 Azure, 컨테이너 및 앱용 마이크로 서비스를 이해하고 지원하기 위해 노력해야 합니다.<br/><br/> 데이터베이스를 마이그레이션하기 위해 Data Migration Service 대신 Data Migration Assistant를 사용하는 경우 Contoso에는 대규모 데이터베이스 마이그레이션을 위해 준비된 인프라가 없습니다.



### <a name="migration-process"></a>마이그레이션 프로세스

1. Contoso는 Windows용 Azure Service Fabric 클러스터를 프로비전합니다.
2. Contoso는 Azure SQL 인스턴스를 프로비전하고, 거기에 SmartHotel 데이터베이스를 마이그레이션합니다.
3. Contoso는 Service Fabric SDK 도구를 사용하여 웹 계층 VM을 Docker 컨테이너로 변환합니다.
4. Azure Service Fabric을 사용하여 Service Fabric 클러스터와 ACR을 연결하고 앱을 배포합니다.

    ![마이그레이션 프로세스](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Azure 서비스

**서비스** | **설명** | **비용**
--- | --- | ---
[DMA(Database Migration Assistant)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | DMA를 사용하여 Azure의 데이터베이스 기능에 영향을 줄 수 있는 호환성 문제를 평가하고 검색합니다. DMA는 SQL 원본과 대상 간의 기능 패리티를 평가하고 성능 및 안정성을 향상하기 위한 방안을 제시합니다. | DMA는 무료로 다운로드할 수 있는 도구입니다.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | 완전히 관리되는 지능적인 관계형 클라우드 데이터베이스 서비스입니다. | 기능, 처리량 및 크기에 따른 비용입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | 모든 유형의 컨테이너 배포용 이미지를 저장합니다. | 기능, 저장소 및 사용 기간에 따른 비용입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | 항상 사용할 수 있고 확장 가능한 분산형 앱을 빌드하고 운영합니다. | 계산 노드의 크기, 위치 및 기간에 따른 비용입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>필수 조건

귀하(및 Contoso)가 이 시나리오를 실행하는 데 필요한 것은 다음과 같습니다.

**요구 사항** | **세부 정보**
--- | ---
**Azure 구독** | 이 시리즈의 첫 번째 문서에서 평가를 수행했을 때 이미 구독이 만들어졌어야 합니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.<br/><br/> 무료 계정을 만드는 경우 구독 관리자로서 모든 작업을 수행할 수 있습니다.<br/><br/> 기존 구독을 사용하고 관리자가 아닌 경우 관리자와 협력하여 소유자 또는 기여자 권한을 할당받아야 합니다.
**Azure 인프라** | Contoso가 Azure 인프라를 설정하는 [방법에 대해 알아보세요](contoso-migration-infrastructure.md).
**개발자 필수 구성 요소** | Contoso는 개발자 워크스테이션에서 다음 도구가 필요합니다.<br/><br/> - [Visual Studio 2017 Community Edition: 버전 15.5](https://www.visualstudio.com/)<br/><br/> - .NET 워크로드를 사용하도록 설정해야 합니다.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [Service Fabric SDK v 3.0 이상](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> Windows 컨테이너를 사용하도록 설정된 - [Docker CE(Windows 10) 또는 Docker EE(Windows Server)](https://docs.docker.com/docker-for-windows/install/).



## <a name="scenario-steps"></a>시나리오 단계

Contoso가 마이그레이션을 실행하는 방법은 다음과 같습니다.

> [!div class="checklist"]
> * **1단계: Azure에서 SQL Database 인스턴스 프로비전**: Contoso는 Azure에서 SQL 인스턴스를 프로비전합니다. 프런트 엔드 VM이 Azure 컨테이너로 마이그레이션되면 앱 웹 프런트 엔드가 있는 컨테이너 인스턴스는 이 데이터베이스를 가리키게 됩니다.
> * **2단계: Azure Service Fabric 프로비전**: Service Fabric 클러스터를 프로비전합니다.
> * **4단계: DMA를 사용하여 데이터베이스 마이그레이션**: Database Migration Assistant를 사용하여 앱 데이터베이스를 마이그레이션합니다.
> * **5단계: 앱을 컨테이너로 변환**: Visual Studio 및 SDK Tools를 사용하여 앱을 컨테이너로 변환합니다.
> * **6단계: 앱 게시**: ACR 및 Service Fabric 클러스터에 앱을 게시합니다.
> * **7단계: 앱 확장**: 앱이 게시되면 Azure 기능을 활용하도록 앱을 확장하고 다시 Azure에 게시합니다.



## <a name="step-1-provision-an-azure-sql-database"></a>1단계: Azure SQL Database 프로비전

1. Azure에서 SQL Database를 만들기로 선택합니다. 

    ![SQL 프로비전](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. 온-프레미스 VM에서 실행되는 데이터베이스와 일치하는 데이터베이스 이름을 지정합니다(**SmartHotel.Registration**). ContosoRG 리소스 그룹에 데이터베이스를 배치합니다. Azure에서 프로덕션 리소스에 사용하는 리소스 그룹입니다.

    ![SQL 프로비전](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. 주 지역에 새 SQL Server 인스턴스를 설정합니다(**sql-smarthotel-eus2**).

    ![SQL 프로비전](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. 해당 서버 및 데이터베이스 요구 사항과 일치하도록 가격 책정 계층을 설정합니다. SQL Server 라이선스가 이미 있으므로 Azure 하이브리드 혜택으로 비용을 절약하도록 선택합니다.
5. 크기 조정의 경우 V 코어 기반 구매를 사용하며, 필요한 요구 사항에 대한 제한을 설정합니다.

    ![SQL 프로비전](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. 그런 다음, 데이터베이스 인스턴스를 만듭니다.

    ![SQL 프로비전](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. 인스턴스가 만들어진 후 데이터베이스를 열고, 마이그레이션에 Database Migration Assistance를 사용할 때 필요한 세부 정보를 기록합니다.

    ![SQL 프로비전](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**도움이 더 필요하세요?**

- SQL Database 프로비전에 대한 [도움말을 보세요](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- V 코어 리소스 제한에 [대해 자세히 알아보세요](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools).



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>2단계: ACR을 만들고 Azure 컨테이너 프로비전

Azure 컨테이너는 웹 VM에서 내보낸 파일을 사용하여 생성됩니다. 컨테이너는 ACR(Azure Container Registry)에 보관됩니다.


1. Contoso는 Azure Portal에서 Container Registry를 만듭니다.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. 레지스트리 이름을 입력하고(**contosoacreus2**), 인프라 리소스에 사용되는 리소스 그룹의 주 지역에 배치합니다. 관리 사용자에 대한 액세스를 사용할 수 있도록 설정하고 프리미엄 SKU로 설정하면 지역 복제를 활용할 수 있습니다.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>3단계: Azure Service Fabric 프로비전

SmartHotel 컨테이너는 Azure Service Fabric 클러스터에서 실행됩니다. Contoso는 다음과 같이 Service Fabric 클러스터를 만듭니다.

1. Azure Marketplace에서 Service Fabric 리소스 만들기

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. **기본**에서 클러스터의 고유 DS 이름과 온-프레미스 VM에 액세스하기 위한 자격 증명을 입력합니다. 주 지역인 미국 동부 2의 프로덕션 리소스 그룹(**ContosoRG**)에 리소스를 배치합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. **노드 유형 구성**에서 노드 유형 이름, 지속성 설정, VM 크기 및 앱 엔드포인트를 입력합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. **키 자격 증명 모음 만들기**에서 인증서를 보관할 새로운 키 자격 증명 모음을 인프라 리소스 그룹에 만듭니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. **액세스 정책**에서 키 자격 증명 모음을 배포할 가상 머신에 액세스할 수 있도록 설정합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. 인증서 이름을 지정합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. 요약 페이지에서 인증서를 다운로드하는 데 사용되는 링크를 복사합니다. Service Fabric 클러스터에 연결하려면 인증서가 필요합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. 유효성 검사에 통과한 후에는 클러스터를 프로비전합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. 인증서 가져오기 마법사에서, 다운로드한 인증서를 개발 머신으로 가져옵니다. 인증서는 클러스터를 인증하는 데 사용됩니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. 클러스터를 프로비전한 후에는 Service Fabric 클러스터 탐색기에 연결합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. 올바른 인증서를 선택해야 합니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Service Fabric Explorer가 로드되고, Contoso 관리자는 클러스터를 관리할 수 있습니다.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-3-migrate-the-database-with-dma"></a>3단계: DMA를 사용하여 데이터베이스 마이그레이션

Contoso는 DMA를 사용하여 SmartHotel 데이터베이스를 마이그레이션합니다.

### <a name="install-dma"></a>DMA 설치

1. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53595)에서 온-프레미스 SQL Server VM(**SQLVM**)으로 도구를 다운로드합니다.
2. VM에서 설치(DownloadMigrationAssistant.msi)를 실행합니다.
3. **마침** 페이지에서 마법사를 마치기 전에 **Microsoft Data Migration Assistant 시작**을 선택합니다.

### <a name="configure-the-firewall"></a>방화벽 구성

Azure SQL Database에 연결하려면 방화벽 규칙이 필요합니다.

1. 데이터베이스의 **방화벽 및 가상 네트워크** 속성에서 Azure 서비스에 대한 액세스를 허용하고, 온-프레미스 SQL Server VM의 클라이언트 IP 주소에 대한 규칙을 추가합니다.
2. 서버 수준 방화벽 규칙이 생성됩니다.

    ![방화벽](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

도움이 더 필요하세요?

Azure SQL Database에 대한 방화벽 규칙을 만들고 관리하는 방법에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules).

### <a name="migrate"></a>마이그레이션

1. DMA에서 새 프로젝트(**SmartHotelDB**)를 만들고 **마이그레이션**을 선택합니다. 
2. 원본 서버 유형을 **SQL Server**로 선택하고, 대상을 **Azure SQL Database**로 지정합니다. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. 마이그레이션 세부 정보에서 **SQLVM**을 원본 서버로 추가하고 **SmartHotel.Registration** 데이터베이스를 추가합니다. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. 인증으로 연결된 것처럼 보이는 오류가 발생합니다. 그러나 문제를 조사한 후 문제는 데이터베이스 이름의 마침표(.)입니다. 해결 방법으로 문제를 해결하기 위해 **SmartHotel-Registration**이라는 이름을 사용하여 새 SQL 데이터베이스를 프로비전하도록 결정했습니다. DMA를 다시 실행할 때 **SmartHotel-Registration**을 선택하고, 마법사를 계속해서 사용할 수 있습니다.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. **개체 선택**에서 데이터베이스 테이블을 선택하고, SQL 스크립트를 생성합니다.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. DMS에서 스크립트를 만든 후 **스키마 배포**를 클릭합니다.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA는 배포가 성공했는지 확인합니다.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. 이제 마이그레이션을 시작합니다.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. 마이그레이션이 완료된 후 Contoso는 데이터베이스가 Azure SQL 인스턴스에서 실행되는지 확인할 수 있습니다.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Azure Portal에서 추가 SQL 데이터베이스 **SmartHotel.Registration**을 삭제합니다.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>4단계: 앱을 컨테이너로 변환

온-프레미스 앱은 기존의 3계층 앱입니다.

- 이 앱은 SQL Server에 연결하는 WebForms 및 WCF 서비스를 포함하고 있습니다.
- 또한 Entity Framework를 사용하여 SQL 데이터베이스의 데이터와 통합되고, WCF 서비스를 통해 데이터를 노출합니다.
- WebForms 응용 프로그램은 WCF 서비스와 상호 작용합니다.

Contoso는 다음과 같이 Visual Studio 및 SDK Tools를 사용하여 앱을 컨테이너로 변환합니다.

1. 리포지토리를 개발자 머신에 로컬로 복제합니다.

    **git clone https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![컨테이너](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. Visual Studio를 사용하여 로컬 리포지토리의 **SmartHotel360-internal-booking-apps\src\Registration** 디렉터리에서 솔루션 파일(SmartHotel.Registration.sln)을 엽니다.  두 개의 앱이 표시됩니다. 하나는 웹 프런트 엔드 SmartHotel.Registration.Web이고 다른 하나는 WCF 서비스 앱 SmartHotel.Registration.WCF입니다.

    ![컨테이너](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. 웹앱을 마우스 오른쪽 단추로 클릭하고 **추가** > **컨테이너 오케스트레이터 지원**을 클릭합니다.
4. **컨테이너 오케스트라 지원 추가**에서 **Service Fabric**을 선택합니다.

    ![컨테이너](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso는 SmartHotel.Registration.WCF 앱에 대해 같은 프로세스를 반복합니다.
6. 이제 Contoso는 솔루션이 어떻게 변경되었는지 확인합니다.

    - 새 앱은 **SmartHotel.RegistrationApplication/** 입니다.
    - 두 서비스 **SmartHotel.Registration.WCF** 및 **SmartHotel.Registration.Web**을 포함하고 있습니다.

    ![컨테이너](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. Visual Studio는 Docker 파일을 만들고 필요한 이미지를 개발자 머신에 로컬로 가져왔습니다.

    ![컨테이너](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. 매니페스트 파일(**ServiceManifest.xml**)이 생성되어 Visual Studio에서 열립니다. 이 파일은 컨테이너를 Azure에 배포할 때 컨테이너 구성 방법을 Service Fabric에 알려줍니다.

    ![컨테이너](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. 또 다른 매니페스트 파일(**ApplicationManifest.xml)에는 컨테이너에 대한 구성 응용 프로그램이 포함되어 있습니다.

    ![컨테이너](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>5단계: 앱 게시


마지막으로, Contoso는 ACR 및 Service Fabric 클러스터에 앱을 게시할 수 있습니다.

> [!NOTE]
> SmartHotel 앱에서 Service Fabric 클러스터와 관련된 몇 가지 사항이 변경되었습니다. [GitHub](https://github.com/Microsoft/SmartHotel360-internal-booking-apps)에서 원래 앱 코드와 현대화된 앱 코드를 다운로드할 수 있습니다. 변경된 파일은 **AppliationModern/ApplicationParameters/Cloud.xml**입니다.


1. Visual Studio에서 앱을 Azure SQL Database에 연결하도록 연결 문자열을 업데이트합니다. 연결 문자열은 Azure Portal을 사용하여 데이터베이스에서 찾을 수 있습니다.

    ![게시](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso는 Visual Studio를 사용하여 Service Fabric에 앱을 게시합니다. Service Fabric 응용 프로그램을 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

    ![게시](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. 구독, 연결 엔드포인트 및 ACR을 선택합니다. 그런 다음, **게시**를 클릭합니다.

    ![게시](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. 배포가 완료되면 SmartHotel은 이제 Service Fabric을 실행합니다.

    ![게시](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. 앱에 연결하기 위해 Contoso는 Service Fabric 노드 앞에서 트래픽을 Azure 부하 분산 장치의 공용 IP 주소로 전달합니다.

    ![게시](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>6단계: 앱을 확장하고 다시 게시

SmartHotel 앱 및 데이터베이스가 Azure에서 실행되자, Contoso는 앱을 확장하려고 합니다.

- Contoso의 개발자는 Service Fabric 클러스터에서 실행될 새로운 .NET Core 응용 프로그램의 프로토타입을 제작합니다.
- 이 앱은 CosmosDB에서 감정 데이터를 가져오는 데 사용될 것입니다.
- 이 데이터는 Serverless Azure Function 및 Cognitive Services Text Analysis API를 사용하여 처리되는 트윗 형식입니다.

### <a name="provision-azure-cosmos-db"></a>Azure Cosmos DB 프로비전

첫 번째 단계로, Contoso는 Azure Cosmos 데이터베이스를 프로비전합니다.

1. Azure Marketplace에서 Azure Cosmos DB 리소스를 만듭니다.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. 데이터베이스 이름(**contososmarthotel**)을 입력하고, SQL API를 선택하고, 프로덕션 리소스 그룹의 주 지역인 미국 동부 2에 리소스를 배치합니다.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. **시작**에서 **데이터 탐색기**를 선택하고 새 컬렉션을 추가합니다.
4. **컬렉션 추가**에서 ID를 입력하고 저장소 용량 및 처리량을 설정합니다.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. 포털에서 새 데이터베이스 > **컬렉션** > **문서**를 열고 **새 문서**를 클릭합니다.
6. 문서 창에 다음 JSON 코드를 붙여넣습니다. 단일 트윗 형태의 샘플 데이터입니다.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Cosmos DB 엔드포인트 및 인증 키를 찾습니다. 앱에서 컬렉션에 연결할 때 사용됩니다. 데이터베이스에서 **키**를 클릭하고, URI 및 기본 키를 메모장에 복사합니다.

    ![Extend](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>감정 앱 업데이트

Cosmos DB가 프로비전되면 Contoso는 Cosmos DB에 연결하도록 앱을 구성할 수 있습니다.

1. Visual Studio에서 솔루션 탐색기를 사용하여 ApplicationModern\ApplicationParameters\cloud.xml 파일을 엽니다.

    ![감정 앱](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. 다음 두 매개 변수를 입력합니다.

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![감정 앱](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>앱 다시 게시

앱을 확장한 후 Contoso는 앱을 Azure에 다시 게시합니다.

1. 포털에서 Service Fabric 앱을 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

    ![다시 게시](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. 구독, 연결 엔드포인트 및 ACR을 선택합니다. 그런 다음, **게시**를 클릭합니다.

    ![다시 게시](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. 배포가 완료되면 SmartHotel은 이제 Service Fabric을 실행합니다. 이제 Service Fabric 관리 콘솔에 세 가지 서비스가 표시됩니다.

    ![다시 게시](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Contoso는 서비스를 클릭하여 SentimentIntegration 앱이 실행 중인지 확인할 수 있습니다.

    ![다시 게시](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>마이그레이션 후 정리

마이그레이션 후 Contoso는 다음 정리 단계를 완료해야 합니다.  

- vCenter 인벤토리에서 온-프레미스 VM을 제거합니다.
- 로컬 백업 작업에서 VM을 제거합니다.
- 내부 설명서를 업데이트하여 SmartHotel 앱의 새 위치를 표시합니다. 데이터베이스는 Azure SQL 데이터베이스에서 실행되고 프런트 엔드는 Service Fabric에서 실행되는 것으로 표시합니다.
- 서비스 해제된 VM과 상호 작용하는 모든 리소스를 검토하고, 새 구성을 반영하도록 모든 관련 설정 또는 설명서를 업데이트합니다.


## <a name="review-the-deployment"></a>배포 검토

Azure에서 마이그레이션된 리소스를 사용하여 Contoso는 새 인프라를 완전하게 운영하고 보호해야 합니다.

### <a name="security"></a>보안

- Contoso는 새 **SmartHotel-Registration** 데이터베이스가 안전한지 확인해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- 특히 인증서에 SSL을 사용하도록 컨테이너를 업데이트해야 합니다.
- Key Vault를 사용하여 Service Fabric 앱의 비밀을 보호하는 방안을 고려해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Backup

- Contoso는 Azure SQL Database에 대한 백업 요구 사항을 검토해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- 데이터베이스에 대한 지역별 장애 조치(failover)를 제공하는 장애 조치(failover) 그룹 구현을 고려해야 합니다. [자세히 알아보기](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- ACR 프리미엄 SKU에 지역 복제를 활용할 수 있습니다. [자세히 알아보기](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- 컨테이너용 웹앱을 사용할 수 있게 되면 Contoso는 주 지역인 미국 동부 2 및 미국 중부에 웹앱을 배포하는 방안을 고려해야 합니다. Contoso는 지역 가동 중단 발생 시 장애 조치(failover)하도록 Traffic Manager를 구성할 수 있습니다.

### <a name="licensing-and-cost-optimization"></a>라이선스 및 비용 최적화

- 모든 리소스가 배포된 후 Contoso는 해당 [인프라 계획](contoso-migration-infrastructure.md#set-up-tagging)을 따라 Azure 태그를 할당해야 합니다.
- 모든 라이선스는 Contoso가 사용하는 PaaS 서비스의 비용으로 빌드됩니다. 이 요금은 EA에서 공제됩니다.
1. Contoso는 Microsoft 자회사인 Cloudyn에서 허가된 Azure Cost Management를 사용하도록 설정합니다. Azure 및 기타 클라우드 리소스를 활용하고 관리하는 데 도움이 되는 다중 클라우드 비용 관리 솔루션입니다.  Azure Cost Management에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/cost-management/overview). 

## <a name="conclusion"></a>결론

이 문서에서 Contoso는 앱 프런트 엔드 VM을 Service Fabric으로 마이그레이션하여 Azure에서 SmartHotel 앱을 리팩터링했습니다. Azure SQL 데이터베이스로 앱 데이터베이스를 마이그레이션했습니다.





